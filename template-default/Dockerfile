# Build stage
FROM node:20-slim AS builder

# Set environment variables
ENV PNPM_HOME="/pnpm"
ENV PATH="$PNPM_HOME:$PATH"
ENV NODE_OPTIONS=--max-old-space-size=8192
ENV TZ=Asia/Shanghai
ENV NPM_CONFIG_REGISTRY=https://registry.npmmirror.com
ENV PNPM_REGISTRY=https://registry.npmmirror.com

# Set npm mirror first
#RUN npm config set registry https://registry.npmmirror.com

# Configure pnpm to use npmmirror registry
#RUN pnpm config set registry https://registry.npmmirror.com

# Enable corepack for pnpm
#RUN corepack enable && corepack prepare pnpm@latest --activate

# Alternative solution
RUN npm install -g pnpm@latest --registry=https://registry.npmmirror.com

# Configure pnpm to use npmmirror registry
#RUN pnpm config set registry https://registry.npmmirror.com

WORKDIR /app

# Copy all files
COPY . .


# Install dependencies and build
RUN --mount=type=cache,id=pnpm,target=/pnpm/store pnpm install --frozen-lockfile
RUN pnpm run build

# Production stage
FROM nginx:stable-alpine

# Set timezone for production image
ENV TZ=Asia/Shanghai

# Copy built files to nginx
COPY --from=builder /app/build /usr/share/nginx/html

# Configure nginx
#RUN echo 'server {\n\
#    listen 8000;\n\
#    server_name localhost;\n\
#    root /usr/share/nginx/html;\n\
#    index index.html;\n\
#    location / {\n\
#        try_files $uri $uri/ /index.html;\n\
#    }\n\
#    # Enable CORS if needed\n\
#    add_header Access-Control-Allow-Origin *;\n\
#    add_header Access-Control-Allow-Methods "GET, POST, OPTIONS";\n\
#    add_header Access-Control-Allow-Headers "DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type";\n\
#}' > /etc/nginx/conf.d/default.conf

#EXPOSE 8000

CMD ["nginx", "-g", "daemon off;"]
