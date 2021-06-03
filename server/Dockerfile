# https://hub.docker.com/_/nginx

FROM nginx:1.21

ARG conf

COPY ${conf} /etc/nginx/nginx.conf

EXPOSE 80