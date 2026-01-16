FROM node:18-alpine AS builder
WORKDIR /app
ENV NODE_OPTIONS=--openssl-legacy-provider

# Install dependencies early so they can be cached between builds.
COPY package.json yarn.lock ./
RUN yarn install --frozen-lockfile

# Copy the rest of the source and build the production assets.
COPY . .
RUN yarn build

FROM node:18-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production

# Only install production dependencies in the final image.
COPY --from=builder /app/package.json ./package.json
COPY --from=builder /app/yarn.lock ./yarn.lock
RUN yarn install --production --frozen-lockfile

# Bring over the built assets and static files.
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/next.config.js ./next.config.js

EXPOSE 3000
CMD ["yarn", "start"]
