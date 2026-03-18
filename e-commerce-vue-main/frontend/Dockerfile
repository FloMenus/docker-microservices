FROM node:20-alpine

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

RUN chown appuser:appgroup /app

COPY --chown=appuser:appgroup package.json package-lock.json* ./

USER appuser

RUN npm install --ignore-scripts

EXPOSE 8080

CMD ["npm", "run", "dev", "--", "--host", "0.0.0.0", "--port", "8080"]
