FROM node:20-alpine AS build
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
# Empty = browser calls same origin; Nginx proxies /api and /health to Node (see NODE_BACKEND_ORIGIN in final stage).
ARG VITE_API_URL=
ENV VITE_API_URL=$VITE_API_URL
RUN npm run build

FROM nginx:1.25-alpine
# docker-compose.yml overrides this to http://node:3000 for local stacks.
# Render: omit build arg to use your deployed Node URL below.
ARG NODE_BACKEND_ORIGIN=https://prem-3.onrender.com
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf.template /tmp/nginx.conf.template
RUN sed "s#BACKEND_ORIGIN#${NODE_BACKEND_ORIGIN}#g" /tmp/nginx.conf.template > /etc/nginx/conf.d/default.conf
EXPOSE 80
