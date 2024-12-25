FROM nginx:1.27

WORKDIR /var/www/html

ENV TZ=Asia/Tokyo

COPY ./infra/backend/web/*.conf /etc/nginx/conf.d/
