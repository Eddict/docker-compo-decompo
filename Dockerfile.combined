FROM python:3.12-alpine
LABEL org.opencontainers.image.source=https://github.com/Red5d/docker-autocompose

# Install Poetry and Node.js
RUN apk add --no-cache poetry nodejs npm

# Install composerize globally
RUN npm install -g composerize@latest --omit=dev --no-audit --no-fund && npm cache clean --force

WORKDIR /usr/src/app

# Copy Python project files
COPY poetry.lock pyproject.toml README.md ./
COPY src ./src

# Install Python dependencies
RUN poetry install

# Default to bash for manual use, but both tools are available
CMD ["/bin/sh"]
