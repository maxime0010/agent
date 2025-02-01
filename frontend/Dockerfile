# ===========================
# Base Stage: Setup Node.js
# ===========================
FROM node:18-alpine AS base

# Enable Corepack (required for Yarn 2+ and PNPM)
RUN corepack enable

# Install dependencies for Alpine Linux
RUN apk add --no-cache libc6-compat

# Set working directory
WORKDIR /app

# ===========================
# Dependencies Stage
# ===========================
FROM base AS deps
WORKDIR /app

# Copy package manager files
COPY package.json pnpm-lock.yaml* ./

# Use PNPM as package manager (not Yarn)
RUN corepack prepare pnpm@latest --activate && \
    pnpm install --frozen-lockfile

# ===========================
# Build Stage
# ===========================
FROM base AS builder
WORKDIR /app

# Copy dependencies from deps stage
COPY --from=deps /app/node_modules ./node_modules

# Copy source code
COPY . .

# Use PNPM for Prisma and Build
RUN pnpm prisma generate
RUN pnpm run build

# ===========================
# Production Runner Stage
# ===========================
FROM base AS runner
WORKDIR /app

# Set environment to production
ENV NODE_ENV production

# Add non-root user for security
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

# Copy public assets and build output
COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

# Use non-root user
USER nextjs

# Expose application port
EXPOSE 3000
ENV PORT 3000

# Start the application
CMD ["node", "server.js"]
