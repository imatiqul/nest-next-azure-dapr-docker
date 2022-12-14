# Install dependencies only when needed
FROM node:16.14.2-alpine as deps
# Check https://github.com/nodejs/docker-node/tree/b4117f9333da4138b03a546ec926ef50a31506c3#nodealpine to understand why libc6-compat might be needed.
RUN apk add --no-cache libc6-compat
WORKDIR /usr/src/app
COPY dist/apps/ai-shop-client-app/package*.json ./
RUN npm install --only=production

# Production image, copy all the files and run next
FROM node:16.14.2-alpine as runner
LABEL maintainer="Atiqul Islam <islam.md.atiqul@gmail.com>"
RUN apk add --no-cache dumb-init
ENV NODE_ENV production
WORKDIR /usr/src/app
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001
COPY dist/apps/ai-shop-client-app/public ./public
COPY --chown=nextjs:nodejs dist/apps/ai-shop-client-app/.next ./.next
COPY --from=deps /usr/src/app/node_modules ./node_modules
COPY --from=deps /usr/src/app/package.json ./package.json
USER nextjs
EXPOSE 4200
ENV PORT 4200
# COPY --chown=node:node ./tools/scripts/entrypoints/api.sh /usr/local/bin/docker-entrypoint.sh
# ENTRYPOINT [ "docker-entrypoint.sh" ]
# Next.js collects completely anonymous telemetry data about general usage.
# Learn more here: https://nextjs.org/telemetry
# Uncomment the following line in case you want to disable telemetry.
# ENV NEXT_TELEMETRY_DISABLED 1
CMD ["dumb-init", "node_modules/.bin/next", "start"]