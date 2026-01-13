---
name: docker-cli
description: Docker and Docker Compose commands for containerization. Use for building images, running containers, managing volumes, and orchestrating services.
allowed-tools: Bash, Read, Edit
user-invocable: true
---

# docker-cli

Docker and Docker Compose commands for containerization, image building, and container orchestration.

## Installation

```bash
# macOS
brew install --cask docker

# Verify
docker --version
docker compose version
```

## Images

### Build
```bash
docker build -t myapp .                      # Build from Dockerfile
docker build -t myapp:v1.0 .                 # With tag
docker build -t myapp -f Dockerfile.prod .   # Custom Dockerfile
docker build --no-cache -t myapp .           # Without cache
docker build --platform linux/amd64 -t myapp .  # Specific platform
```

### List & Remove
```bash
docker images                    # List images
docker images -a                 # Include intermediate
docker rmi myapp                 # Remove image
docker rmi $(docker images -q)   # Remove all images
docker image prune               # Remove dangling images
docker image prune -a            # Remove unused images
```

### Push to Registry
```bash
docker login                                 # Login to Docker Hub
docker tag myapp username/myapp:v1.0        # Tag for registry
docker push username/myapp:v1.0             # Push
docker pull username/myapp:v1.0             # Pull
```

## Containers

### Run
```bash
docker run myapp                             # Run container
docker run -d myapp                          # Detached (background)
docker run -p 3000:3000 myapp               # Port mapping
docker run -p 3000:3000 -p 5432:5432 myapp  # Multiple ports
docker run --name mycontainer myapp          # Named container
docker run -e NODE_ENV=production myapp      # Environment variable
docker run --env-file .env myapp             # Env file
docker run -v $(pwd):/app myapp              # Mount volume
docker run -it myapp /bin/sh                 # Interactive shell
docker run --rm myapp                        # Remove after exit
```

### Common Run Patterns
```bash
# Web app with hot reload
docker run -d -p 3000:3000 -v $(pwd):/app --name dev myapp

# Database
docker run -d -p 5432:5432 -e POSTGRES_PASSWORD=secret --name db postgres:15

# One-off command
docker run --rm myapp npm test
```

### Manage
```bash
docker ps                        # Running containers
docker ps -a                     # All containers
docker stop mycontainer          # Stop container
docker start mycontainer         # Start stopped container
docker restart mycontainer       # Restart container
docker rm mycontainer            # Remove container
docker rm -f mycontainer         # Force remove running
docker rm $(docker ps -aq)       # Remove all containers
```

### Logs & Debug
```bash
docker logs mycontainer          # View logs
docker logs -f mycontainer       # Follow logs
docker logs --tail 100 mycontainer  # Last 100 lines
docker exec -it mycontainer sh   # Shell into container
docker exec mycontainer ls /app  # Run command
docker inspect mycontainer       # Container details
docker stats                     # Resource usage
docker top mycontainer           # Running processes
```

## Volumes

```bash
docker volume create myvolume    # Create volume
docker volume ls                 # List volumes
docker volume inspect myvolume   # Volume details
docker volume rm myvolume        # Remove volume
docker volume prune              # Remove unused volumes

# Mount volume
docker run -v myvolume:/data myapp
docker run -v $(pwd)/data:/data myapp  # Bind mount
```

## Networks

```bash
docker network create mynetwork  # Create network
docker network ls                # List networks
docker network inspect mynetwork # Network details
docker network rm mynetwork      # Remove network

# Connect container to network
docker run --network mynetwork myapp
docker network connect mynetwork mycontainer
```

## Docker Compose

### Basic Commands
```bash
docker compose up                # Start services
docker compose up -d             # Detached mode
docker compose up --build        # Rebuild images
docker compose down              # Stop and remove
docker compose down -v           # Also remove volumes
docker compose stop              # Stop services
docker compose start             # Start services
docker compose restart           # Restart services
docker compose ps                # List services
docker compose logs              # View logs
docker compose logs -f api       # Follow specific service
docker compose exec api sh       # Shell into service
```

### Example docker-compose.yml
```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgresql://postgres:secret@db:5432/mydb
    volumes:
      - .:/app
      - /app/node_modules
    depends_on:
      - db
    command: npm run dev

  db:
    image: postgres:15
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

### Production docker-compose.prod.yml
```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile.prod
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
    restart: unless-stopped
```

## Dockerfile Examples

### Node.js
```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .

EXPOSE 3000
CMD ["npm", "start"]
```

### Multi-stage (Production)
```dockerfile
# Build stage
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production stage
FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package*.json ./

ENV NODE_ENV=production
EXPOSE 3000
CMD ["npm", "start"]
```

### Next.js
```dockerfile
FROM node:20-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci

FROM node:20-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static

EXPOSE 3000
CMD ["node", "server.js"]
```

## .dockerignore
```
node_modules
.next
.git
.env*.local
*.log
dist
coverage
```

## Cleanup

```bash
docker system df                 # Disk usage
docker system prune              # Remove unused data
docker system prune -a           # Remove all unused
docker system prune --volumes    # Include volumes
```

## Common Workflows

### Development Setup
```bash
docker compose up -d db          # Start only database
npm run dev                      # Run app locally
```

### Full Stack Dev
```bash
docker compose up --build
# App at localhost:3000, DB at localhost:5432
```

### Production Build & Test
```bash
docker build -t myapp:prod -f Dockerfile.prod .
docker run --rm -p 3000:3000 myapp:prod
```

### Deploy to Registry
```bash
docker build -t ghcr.io/username/myapp:v1.0 .
docker push ghcr.io/username/myapp:v1.0
```

## Troubleshooting

### Container won't start
```bash
docker logs mycontainer          # Check logs
docker inspect mycontainer       # Check config
```

### Port already in use
```bash
lsof -i :3000                    # Find process
kill -9 <PID>                    # Kill process
```

### Out of disk space
```bash
docker system prune -a --volumes
```

### Permission denied
```bash
# Add user to docker group (Linux)
sudo usermod -aG docker $USER
```
