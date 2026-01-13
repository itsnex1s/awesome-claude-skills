---
name: database-cli
description: Database management with Prisma ORM. Use for migrations, schema management, seeding, and database queries.
allowed-tools: Bash, Read, Edit
user-invocable: true
---

# database-cli

Database management with Prisma ORM - migrations, schema management, seeding, and database operations.

## Installation

```bash
npm install prisma --save-dev
npm install @prisma/client
npx prisma init
```

## Quick Reference

```bash
npx prisma generate          # Generate Prisma Client
npx prisma db push           # Push schema to DB (dev)
npx prisma migrate dev       # Create and apply migration
npx prisma migrate deploy    # Apply migrations (prod)
npx prisma studio            # Open GUI browser
npx prisma db seed           # Run seed script
npx prisma format            # Format schema file
```

## Schema Management

### prisma/schema.prisma
```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String?
  password  String
  role      Role     @default(USER)
  posts     Post[]
  profile   Profile?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  @@index([email])
}

model Post {
  id        String   @id @default(cuid())
  title     String
  content   String?
  published Boolean  @default(false)
  author    User     @relation(fields: [authorId], references: [id], onDelete: Cascade)
  authorId  String
  tags      Tag[]
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  @@index([authorId])
}

model Profile {
  id     String  @id @default(cuid())
  bio    String?
  avatar String?
  user   User    @relation(fields: [userId], references: [id], onDelete: Cascade)
  userId String  @unique
}

model Tag {
  id    String @id @default(cuid())
  name  String @unique
  posts Post[]
}

enum Role {
  USER
  ADMIN
}
```

## Migrations

### Development
```bash
# Create migration from schema changes
npx prisma migrate dev --name init
npx prisma migrate dev --name add_user_role

# Apply without creating migration (prototyping)
npx prisma db push

# Reset database (drops all data!)
npx prisma migrate reset
```

### Production
```bash
# Apply pending migrations
npx prisma migrate deploy

# Check migration status
npx prisma migrate status
```

### Migration Commands
```bash
npx prisma migrate dev              # Create & apply migration
npx prisma migrate deploy           # Apply migrations (CI/CD)
npx prisma migrate reset            # Reset database
npx prisma migrate status           # Check status
npx prisma migrate resolve --applied "migration_name"  # Mark as applied
```

## Generate Client

```bash
npx prisma generate                 # Generate after schema change
```

After generating, restart your dev server or TypeScript server.

## Prisma Studio

```bash
npx prisma studio                   # Opens at localhost:5555
npx prisma studio --port 5556       # Custom port
```

## Seeding

### package.json
```json
{
  "prisma": {
    "seed": "tsx prisma/seed.ts"
  }
}
```

### prisma/seed.ts
```typescript
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()

async function main() {
  // Clean existing data
  await prisma.post.deleteMany()
  await prisma.user.deleteMany()

  // Create users
  const admin = await prisma.user.create({
    data: {
      email: 'admin@example.com',
      name: 'Admin User',
      password: 'hashed_password',
      role: 'ADMIN',
      profile: {
        create: {
          bio: 'System administrator',
        },
      },
    },
  })

  const user = await prisma.user.create({
    data: {
      email: 'user@example.com',
      name: 'Regular User',
      password: 'hashed_password',
      posts: {
        create: [
          {
            title: 'First Post',
            content: 'Hello World!',
            published: true,
          },
          {
            title: 'Draft Post',
            content: 'Work in progress...',
            published: false,
          },
        ],
      },
    },
  })

  console.log({ admin, user })
}

main()
  .catch((e) => {
    console.error(e)
    process.exit(1)
  })
  .finally(async () => {
    await prisma.$disconnect()
  })
```

### Run Seed
```bash
npx prisma db seed
```

## Common Queries

### Create
```typescript
// Single record
const user = await prisma.user.create({
  data: {
    email: 'test@example.com',
    name: 'Test User',
    password: 'hashed',
  },
})

// With relations
const userWithPosts = await prisma.user.create({
  data: {
    email: 'author@example.com',
    name: 'Author',
    password: 'hashed',
    posts: {
      create: [
        { title: 'Post 1', content: 'Content 1' },
        { title: 'Post 2', content: 'Content 2' },
      ],
    },
  },
  include: { posts: true },
})

// Many records
const users = await prisma.user.createMany({
  data: [
    { email: 'a@test.com', name: 'A', password: 'hash' },
    { email: 'b@test.com', name: 'B', password: 'hash' },
  ],
})
```

### Read
```typescript
// Find unique
const user = await prisma.user.findUnique({
  where: { email: 'test@example.com' },
})

// Find first
const post = await prisma.post.findFirst({
  where: { published: true },
})

// Find many
const users = await prisma.user.findMany({
  where: { role: 'USER' },
  orderBy: { createdAt: 'desc' },
  take: 10,
  skip: 0,
})

// With relations
const userWithPosts = await prisma.user.findUnique({
  where: { id: userId },
  include: {
    posts: {
      where: { published: true },
      orderBy: { createdAt: 'desc' },
    },
    profile: true,
  },
})

// Select specific fields
const emails = await prisma.user.findMany({
  select: { email: true, name: true },
})
```

### Update
```typescript
// Single record
const user = await prisma.user.update({
  where: { id: userId },
  data: { name: 'New Name' },
})

// Upsert
const user = await prisma.user.upsert({
  where: { email: 'test@example.com' },
  update: { name: 'Updated' },
  create: { email: 'test@example.com', name: 'New', password: 'hash' },
})

// Many records
const updated = await prisma.user.updateMany({
  where: { role: 'USER' },
  data: { role: 'ADMIN' },
})
```

### Delete
```typescript
// Single record
const user = await prisma.user.delete({
  where: { id: userId },
})

// Many records
const deleted = await prisma.post.deleteMany({
  where: { published: false },
})
```

### Filtering
```typescript
// Complex where
const posts = await prisma.post.findMany({
  where: {
    AND: [
      { published: true },
      { title: { contains: 'prisma', mode: 'insensitive' } },
    ],
    OR: [
      { author: { email: { endsWith: '@company.com' } } },
      { tags: { some: { name: 'featured' } } },
    ],
    NOT: { authorId: excludedUserId },
  },
})
```

## Database URL Examples

```bash
# PostgreSQL
DATABASE_URL="postgresql://user:password@localhost:5432/mydb?schema=public"

# MySQL
DATABASE_URL="mysql://user:password@localhost:3306/mydb"

# SQLite
DATABASE_URL="file:./dev.db"

# MongoDB
DATABASE_URL="mongodb+srv://user:password@cluster.mongodb.net/mydb"
```

## Introspection

```bash
# Generate schema from existing database
npx prisma db pull
```

## Troubleshooting

### Reset everything
```bash
npx prisma migrate reset --force
```

### Client out of sync
```bash
npx prisma generate
# Restart TypeScript server
```

### Migration drift
```bash
npx prisma migrate diff \
  --from-schema-datamodel prisma/schema.prisma \
  --to-schema-datasource prisma/schema.prisma
```

### Check database connection
```bash
npx prisma db execute --stdin <<< "SELECT 1"
```

## Production Checklist

1. Run `npx prisma migrate deploy` in CI/CD
2. Don't use `db push` in production
3. Set `DATABASE_URL` in environment
4. Enable connection pooling for serverless
5. Use transactions for related operations
