FROM nginx:1.27

WORKDIR /workspace

ENV TZ=Asia/Tokyo

COPY ./infra/backend/web/*.conf /etc/nginx/conf.d/
