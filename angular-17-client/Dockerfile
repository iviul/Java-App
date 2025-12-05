# Build stage
ARG NODE_VERSION=18.20.1
FROM node:${NODE_VERSION}-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci 
COPY . .
ARG NODE_ENV=production
ENV NODE_ENV=${NODE_ENV}
RUN npm run build -- --configuration production

# Production stage
FROM nginx:1.27-alpine AS runtime
COPY nginx.conf /etc/nginx/nginx.conf
COPY --from=builder /app/dist/angular-17-crud/browser/ /usr/share/nginx/html/

# Create directories and fix permissions
RUN mkdir -p /var/cache/nginx/client_temp && \
    mkdir -p /var/run/nginx && \
    chmod -R 755 /usr/share/nginx/html && \
    chmod -R 777 /var/cache/nginx && \
    chmod -R 777 /var/run && \
    chmod 777 /var/log/nginx

EXPOSE 80
STOPSIGNAL SIGTERM

HEALTHCHECK --interval=30s --timeout=5s --start-period=5s --retries=3 \
  CMD wget -q --spider http://localhost/ || exit 1

  CMD ["nginx", "-g", "daemon off;"]