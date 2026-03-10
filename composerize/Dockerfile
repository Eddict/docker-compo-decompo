FROM node:22-alpine3.20
RUN npm install -g composerize@latest --omit=dev --no-audit --no-fund && npm cache clean --force
USER node
ENTRYPOINT ["composerize"]
CMD ["--help"]
