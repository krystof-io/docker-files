FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine

# Add nginx user and configure permissions
RUN addgroup -g 2000 nginx-group && \
    adduser -u 2000 -G nginx-group -s /bin/sh -D nginx-user && \
    mkdir -p /tmp/nginx/cache /tmp/nginx/run && \
    chown -R nginx-user:nginx-group /tmp/nginx

# Install envsubst
RUN apk add --no-cache gettext

# Copy the built app
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/templates/default.conf.template

# Switch to non-root user
USER 2000

ENV BACKEND_URL=localhost:8080
EXPOSE 8080

# Use NGINX's built-in template processing
CMD ["sh", "-c", "envsubst '${BACKEND_URL}' < /etc/nginx/templates/default.conf.template > /tmp/nginx/nginx.conf && nginx -c /tmp/nginx/nginx.conf -g 'daemon off;'"]