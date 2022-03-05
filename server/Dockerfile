# https://github.com/linianhui/docker/pkgs/container/nginx

FROM ghcr.io/linianhui/nginx:1.21

ARG HTTP_VERSION

COPY nginx.conf /etc/nginx/nginx.conf

RUN sed -i "s/http2/${HTTP_VERSION}/g" /etc/nginx/nginx.conf

RUN cat /etc/nginx/nginx.conf

EXPOSE 80