---
name: docker
description: Guidelines for writing efficient and secure Dockerfiles
globs: "**/Dockerfile*"
alwaysApply: false
---

## When to use this Skill

- Use Docker for containerization , multiple stage build and cloud-native development.

## Requirements

- Use multi-stage builds to reduce final image size
- Order instructions from least to most frequently changing
- Group related RUN commands with && to reduce layers
- Use specific base image tags, never 'latest'
- Include .dockerignore to exclude unnecessary files
- Use a builder stage for compilation, dependency installation, and other build-time operations
- Use a separate runtime stage that only includes what's needed to run the application
- Copy only the necessary artifacts from the builder stage to the runtime stage
- Use meaningful stage names with the `AS` keyword (e.g., `FROM node:18 AS builder`)
- Place stages in logical order: dependencies → build → test → runtime

## Base Images

- Start with official, minimal base images when possible
- Specify exact version tags to ensure reproducible builds (e.g., `python:3.11-slim` not just `python`)
- Consider distroless images for runtime stages where appropriate
- Use Alpine-based images for smaller footprints when compatible with your application
- Ensure the runtime image has the minimal necessary dependencies

## Security Best Practices

- Avoid running containers as root - use `USER` instruction to specify a non-root user
- Use official or verified base images
- Scan images for vulnerabilities regularly
- Don't store secrets in images
- Use COPY instead of ADD unless extracting archives
- Use multi-stage builds to avoid including build secrets in the final image
- Remove build tools and unnecessary packages from the final image
- Scan the final image for vulnerabilities
- Use multi-stage builds to avoid including build secrets in the final image

## Performance Optimization

- Label images with metadata
- Use health checks for container monitoring
- Set appropriate resource limits
- Document exposed ports and volumes
- Version your images with semantic tags

- Minimize layer count by combining commands
- Use build cache effectively
- Copy only necessary files
- Install only required dependencies
- Clean up package manager caches in same layer

## Build Patterns

```dockerfile
# Good: Multi-stage build example
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

FROM node:18-alpine
RUN addgroup -g 1001 -S nodejs && adduser -S nodejs -u 1001
WORKDIR /app
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --chown=nodejs:nodejs . .
USER nodejs
EXPOSE 3000
CMD ["node", "server.js"]
```
