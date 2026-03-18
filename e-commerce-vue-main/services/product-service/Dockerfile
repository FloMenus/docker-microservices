FROM node:20-alpine

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

RUN chown appuser:appgroup /app

COPY --chown=appuser:appgroup package.json ./

USER appuser

RUN npm install --ignore-scripts

EXPOSE 3000

CMD ["npm", "run", "dev"]
