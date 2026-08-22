FROM node:26.7.0-slim@sha256:4ebb5ace66f15a24c14c492e01a8beeed4fddf970a856109f5126e703e5fe503 AS builder

WORKDIR /app

# Copy package files
COPY package.json ./

# First install dependencies without using the lockfile
# Skip postinstall scripts to avoid @swc/core segfault
RUN npm install --no-package-lock --ignore-scripts

# Copy application code
COPY . .

# Build the application in production mode
# Increase Node memory and use npx to run vite directly
RUN NODE_OPTIONS="--max-old-space-size=4096" npx vite build

# Runtime stage
FROM node:26.7.0-alpine@sha256:aadf416b2cdce311a8811ba3f0608a61b77dbf997500e2eafe781b51f6a0b019

WORKDIR /app

RUN apk upgrade --no-cache

# Install serve for hosting the built app
RUN npm install -g serve

# Copy built app from builder stage
COPY --from=builder /app/dist /app/dist
COPY docker-entrypoint.sh /usr/local/bin/
COPY serve.json /app/serve.json

# Make entrypoint executable
RUN chmod +x /usr/local/bin/docker-entrypoint.sh

# Use entrypoint script for runtime configuration
ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]
