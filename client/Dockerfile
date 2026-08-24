FROM node:20-alpine AS build

WORKDIR /app

# Receive Vite API URL from Docker build
ARG VITE_API_URL
ENV VITE_API_URL=$VITE_API_URL

# Install dependencies
COPY package*.json ./
RUN npm ci

# Copy application source
COPY . .

# Build React/Vite application
RUN npm run build

# Production web server
FROM nginx:alpine

# Copy built frontend
COPY --from=build /app/dist /usr/share/nginx/html

# Custom Nginx configuration
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]