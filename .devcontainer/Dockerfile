FROM ubuntu:20.04

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
ARG PHP_VERSION

# Ensure apt is in non-interactive to avoid prompts
ENV DEBIAN_FRONTEND=noninteractive

# Update repositories index
RUN apt update --fix-missing && apt upgrade -y

# Install util tools
RUN ln -snf /usr/share/zoneinfo/UTC /etc/localtime && echo "UTC" > /etc/timezone
RUN apt install -y apt-utils git rsync nano vim unzip curl wget software-properties-common mysql-client

# Install dependencies.
RUN apt-get -y update --no-install-recommends \
    && apt-get -y install --no-install-recommends \
    build-essential \
    curl \
    ca-certificates \
    apt-utils \
    dialog \
    git \
    vim \
    && apt-get autoremove -y \
    && apt-get clean -y


# Install Ondrej PHP Multiversions
RUN add-apt-repository ppa:ondrej/apache2
RUN add-apt-repository ppa:ondrej/php
RUN apt-get update
RUN apt-get install -y php$PHP_VERSION

# Install Apache
RUN apt-get install -y apache2

# Install PHP extensions
RUN apt-get install -y -m   php$PHP_VERSION-cli \
                            php$PHP_VERSION-dev \
                            php$PHP_VERSION-common \
                            php$PHP_VERSION-json \
                            php$PHP_VERSION-intl \
                            php$PHP_VERSION-curl \
                            php$PHP_VERSION-sqlite3 \
                            php$PHP_VERSION-mysql \
                            php$PHP_VERSION-gd \
                            php$PHP_VERSION-imagick \
                            php$PHP_VERSION-ldap \
                            php$PHP_VERSION-soap\
                            php$PHP_VERSION-zip \
                            php$PHP_VERSION-mbstring \
                            php$PHP_VERSION-bcmath \
                            php$PHP_VERSION-xml \
                            php$PHP_VERSION-imap \
                            php$PHP_VERSION-bz2 

# Add Node.js repository.
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -

# Install Node.js.
RUN apt-get -y update --no-install-recommends \
    && apt-get -y install --no-install-recommends nodejs \
    && apt-get autoremove -y \
    && apt-get clean -y

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin/ --filename=composer

# Configure path installer composer
RUN PATH=$(composer global config bin-dir --absolute --quiet):$PATH

#Install command lavarel global
RUN composer global require laravel/installer

# Config Apache
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2

RUN chown www-data:www-data -R /var/www
RUN chmod -R 755 /var/www

# COPY config/000-default.conf /etc/apache2/sites-available/000-default.conf
# COPY config/php.ini /etc/php/$PHP_VERSION/apache2/php.ini
RUN a2enmod rewrite headers
RUN service apache2 start

WORKDIR /var/www/html

EXPOSE 80
EXPOSE 443

# Create the user.
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME

RUN usermod -aG sudo $USERNAME

ENV DEBIAN_FRONTEND=dialog

USER $USERNAME
