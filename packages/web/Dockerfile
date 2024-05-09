FROM node:22-alpine as base

RUN corepack enable

ENV PNPM_HOME="/var/lib/pnpm"
ENV PATH="$PNPM_HOME:$PATH"
ENV PORT=8080

# BUILDER ---------------------------------------------------------------------

FROM base AS builder
WORKDIR /app

COPY package.json pnpm-lock.yaml pnpm-workspace.yaml .
COPY packages/web ./packages/web

RUN pnpm install --frozen-lockfile

WORKDIR /app/packages/web

RUN pnpm run build

# RUNNER ----------------------------------------------------------------------

FROM base AS runner

WORKDIR /app

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

USER nextjs

COPY --from=builder /app/packages/web/.next/standalone /app

# Copy assets
COPY --from=builder /app/packages/web/public /app/packages/web/public
COPY --from=builder /app/packages/web/.next/static /app/packages/web/.next/static

CMD node ./packages/web/server.js