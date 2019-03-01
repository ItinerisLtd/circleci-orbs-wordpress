##############################################################################################################
# GitHub: https://github.com/ItinerisLtd/circleci-orbs-wordpress/blob/master/src/percy-cypress-wp/Dockerfile #
##############################################################################################################

# TODO: Build from multiple cypress base images
FROM cypress/browsers:chrome69

ARG PHP_VERSION=7.3

ARG DEBIAN_FRONTEND=noninteractive

### CircleCI
# See: https://circleci.com/docs/2.0/custom-images/#required-tools-for-primary-containers
# See: https://github.com/CircleCI-Public/circleci-dockerfiles/blob/b95513ae29e324dcdabb0d2a8248bcf963c226ba/php/images/7.3.2-fpm-stretch/Dockerfile#L15
RUN apt-get -q update && \
    apt-get install -q -y --no-install-recommends \
    git mercurial xvfb apt \
    locales sudo openssh-client ca-certificates tar gzip parallel \
    net-tools netcat unzip zip bzip2 gnupg curl wget && \
    apt-get clean && apt-get -y autoremove && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN \
    # Set timezone to UTC by default
    ln -sf /usr/share/zoneinfo/Etc/UTC /etc/localtime && \
    # Use unicode
    locale-gen C.UTF-8 || true

ENV LANG=C.UTF-8

### PHP
# See https://linuxize.com/post/how-to-install-php-on-debian-9/
# See https://github.com/roots/trellis/blob/005577460c9229329ad67157db32c1d35bb6a0eb/roles/php/defaults/main.yml
# TODO: Build multiple PHP versions
RUN apt-get -q update && \
    apt-get install -q -y --no-install-recommends apt-transport-https ca-certificates curl software-properties-common curl && \
    curl -fsSL https://packages.sury.org/php/apt.gpg | sudo apt-key add - && \
    add-apt-repository "deb https://packages.sury.org/php/ $(lsb_release -cs) main" && \
    apt-get -q update && \
    apt-get install -q -y --no-install-recommends \
    php${PHP_VERSION}-cli \
    php${PHP_VERSION}-common \
    php${PHP_VERSION}-curl \
    php${PHP_VERSION}-dev \
    php${PHP_VERSION}-fpm \
    php${PHP_VERSION}-gd \
    php${PHP_VERSION}-mbstring \
    php${PHP_VERSION}-mysql \
    php${PHP_VERSION}-opcache \
    php${PHP_VERSION}-xml \
    php${PHP_VERSION}-xmlrpc \
    php${PHP_VERSION}-zip && \
    apt-get clean && apt-get -y autoremove && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

### WP CLI
RUN \
    # Download
    wget https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar && \
    wget https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar.asc && \
    wget https://raw.githubusercontent.com/wp-cli/builds/gh-pages/wp-cli.pgp && \
    # Verify PGP signature
    gpg --dearmor wp-cli.pgp && \
    gpg --lock-never --no-default-keyring --keyring ./wp-cli.pgp.gpg --verify wp-cli.phar.asc wp-cli.phar && \
    # Install
    sudo chmod +x wp-cli.phar && \
    sudo mv wp-cli.phar /usr/local/bin/wp && \
    rm wp-cli.*

### MySQL
RUN apt-get -q update && \
    apt-get install -q -y --no-install-recommends mariadb-client && \
    apt-get clean && apt-get -y autoremove && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

### Speed up yarn install
RUN yarn global add wait-on cypress @percy/cypress

# Basic smoke test
RUN echo 'node --version' && node --version && \
    echo 'npm --version' && npm --version && \
    echo 'yarn versions' && yarn versions && \
    echo 'npx --version' && npx --version && \
    echo 'php --version' && php --version && \
    echo 'wp --info' && wp --info && \
    echo 'which mysql' && which mysql

# Define default command.
CMD ["bash"]
