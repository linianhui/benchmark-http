# https://hub.docker.com/_/nginx

FROM nginx:1.21

ARG HTTP_VERSION

COPY nginx.conf /etc/nginx/nginx.conf

RUN sed -i "s/http2/${HTTP_VERSION}/g" /etc/nginx/nginx.conf

RUN cat /etc/nginx/nginx.conf

EXPOSE 80