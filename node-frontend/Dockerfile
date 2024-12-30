FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
RUN apk add --no-cache gettext

COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf.template

# Create entrypoint script using a separate RUN with explicit content
RUN echo '#!/bin/sh' > /docker-entrypoint.sh && \
    echo 'envsubst "\${BACKEND_URL}" < /etc/nginx/conf.d/default.conf.template > /etc/nginx/conf.d/default.conf' >> /docker-entrypoint.sh && \
    echo 'nginx -g "daemon off;"' >> /docker-entrypoint.sh && \
    chmod +x /docker-entrypoint.sh

ENV BACKEND_URL=localhost:8080

EXPOSE 80
CMD ["/docker-entrypoint.sh"]