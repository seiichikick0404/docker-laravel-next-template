FROM php:8.3-fpm-bullseye AS base

WORKDIR /workspace

# timezone environment を日本時間に変更
ENV TZ=Asia/Tokyo \
  # locale
  LANG=en_US.UTF-8 \
  LANGUAGE=en_US:en \
  LC_ALL=en_US.UTF-8 \
  # composer environment
  COMPOSER_HOME=/composer

ARG UID=1000
ARG GID=1000

COPY --from=composer:2.7 /usr/bin/composer /usr/bin/composer

# hadolint ignore=DL3008
RUN <<EOF
  apt-get update
  # tzdata をインストールしてからタイムゾーンを設定
  apt-get -y install --no-install-recommends \
    locales \
    git \
    unzip \
    libzip-dev \
    libicu-dev \
    libonig-dev \
    default-mysql-client \
    curl \
    tzdata

  # タイムゾーンを Asia/Tokyo にリンク＆設定
  ln -snf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
  echo "Asia/Tokyo" > /etc/timezone

  # ロケール設定
  locale-gen en_US.UTF-8
  localedef -f UTF-8 -i en_US en_US.UTF-8

  # PHP拡張
  docker-php-ext-install \
    intl \
    pdo_mysql \
    zip \
    bcmath

  # permission denied bind mount in Linux environment
  groupadd --gid $GID phper
  useradd --uid $UID --gid $GID phper
  mkdir /composer
  mkdir -p /home/phper/.config/psysh
  chown phper:phper /composer
  chown phper:phper /workspace
  chown phper:phper /home/phper/.config/psysh

  # Install Node.js and npm
  curl -fsSL https://deb.nodesource.com/setup_lts.x | bash -
  apt-get install -y nodejs

  apt-get clean
  rm -rf /var/lib/apt/lists/*
EOF

##
# 開発用 (Xdebug なし)
#
FROM base AS development
COPY ./infra/docker/php/php.development.ini /usr/local/etc/php/php.ini
USER phper

##
# 開発用 (Xdebug 有り)
#
FROM base AS development-xdebug

RUN <<EOF
  pecl install xdebug
  docker-php-ext-enable xdebug
EOF

COPY ./infra/docker/php/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

USER phper

##
# 本番用イメージ (デプロイ用)
#
FROM base AS deploy

COPY ./infra/docker/php/php.deploy.ini /usr/local/etc/php/php.ini
COPY --chown=phper:phper ./src /workspace

USER phper

RUN <<EOF
  composer install --quiet --no-interaction --no-ansi --no-dev --no-scripts --no-progress --prefer-dist
  composer dump-autoload --optimize
  chmod -R 777 storage bootstrap/cache
  php artisan optimize:clear
  php artisan optimize
EOF
