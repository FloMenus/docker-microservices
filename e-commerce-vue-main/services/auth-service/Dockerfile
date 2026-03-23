FROM node:20-alpine AS deps

WORKDIR /app
COPY package.json ./
RUN npm install --omit=dev --ignore-scripts

FROM node:20-alpine

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

COPY --from=deps --chown=appuser:appgroup /app/node_modules ./node_modules
COPY --chown=appuser:appgroup src/ ./src/
COPY --chown=appuser:appgroup package.json ./

USER appuser

EXPOSE 3001

CMD ["node", "src/app.js"]
