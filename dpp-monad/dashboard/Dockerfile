FROM node:22-alpine AS build

WORKDIR /app
COPY dpp-monad/dashboard/package.json dpp-monad/dashboard/package-lock.json ./
RUN npm ci
COPY dpp-monad/dashboard ./
ARG VITE_API_URL
ENV VITE_API_URL=${VITE_API_URL}
RUN npm run build

FROM nginx:1.27-alpine
COPY dpp-monad/dashboard/nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 8080
