# Build stage: build decomposerize from source
FROM node:22-alpine3.21 AS build
WORKDIR /build

# Enable corepack and prepare yarn
RUN corepack enable \
  && corepack prepare yarn@1.22.19 --activate
  # && corepack prepare yarn@stable --activate

# Copy entire decomposerize workspace so Yarn sees all manifests
COPY decomposerize/ .

# right before RUN yarn install --immutable
RUN echo "=== files in /build ===" && ls -la \
 && echo "=== package.json ===" && cat package.json \
 && echo "=== yarn.lock head ===" && head -n 40 yarn.lock

# Install deps (Yarn v3+ prefers --immutable)
# RUN yarn install --immutable
# Install deps (Yarn v1 uses --frozen-lockfile)
RUN yarn install --frozen-lockfile

# Build and install globally
RUN yarn build \
  && npm install -g . \
  && npm cache clean --force

# Final stage: runtime image without Node build tools
FROM python:3.13-alpine3.21
LABEL org.opencontainers.image.source=https://github.com/Eddict/docker-autocompose

# Install Poetry
RUN apk add --no-cache python3 py3-pip nodejs \
  && python3 -m pip install --no-cache-dir poetry \
  && poetry --version
  # && python3 -m pip install --no-cache-dir "poetry==1.8.1"

# Copy decomposerize runtime files and binary from build stage
COPY --from=build /usr/local/lib/node_modules/decomposerize /usr/local/lib/node_modules/decomposerize
COPY --from=build /usr/local/bin/decomposerize /usr/local/bin/decomposerize

# Optional smoke test to ensure CLI works
RUN /usr/local/bin/decomposerize --version

WORKDIR /usr/src/app
# Copy Python project files
COPY poetry.lock pyproject.toml README.md ./
COPY src ./src
# Install Python dependencies
RUN poetry install --no-interaction --no-ansi

COPY docker/combined/entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
CMD ["/bin/sh"]
