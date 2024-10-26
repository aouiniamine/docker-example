###################
# BASE FOR PNPM
###################
FROM node:alpine AS base
ENV PNPM_HOME="/pnpm"
ENV PATH="$PNPM_HOME:$PATH"
RUN corepack enable
COPY . /app
WORKDIR /app

###################
# DEPS FOR PRODUCTION 
###################
FROM base AS prod-deps

# Install app production packages
RUN --mount=type=cache,id=pnpm,target=/pnpm/store pnpm install --prod --frozen-lockfile

# Use the node user from the image (instead of the root user)
USER node

###################
# BUILD FOR PRODUCTION
###################

FROM base AS build

# Install app packages for build stage
RUN --mount=type=cache,id=pnpm,target=/pnpm/store pnpm install --frozen-lockfile

# Run the build command which creates the production bundle
RUN pnpm run build

USER node

###################
# PRODUCTION
###################

FROM node:alpine AS production

# Copy the bundled code from the build stage to the production image
COPY --chown=node:node --from=prod-deps /app/node_modules ./node_modules
COPY --chown=node:node --from=build /app/dist ./dist

ENV NODE_ENV=production
EXPOSE 3000
# Start the server using the production build
CMD [ "node", "dist/main.js" ]
