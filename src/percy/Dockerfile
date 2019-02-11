ARG PHP_VERSION=7.3

##############
# ruby-build #
##############

# Use the same php docker image as base to avoid surprises
FROM "circleci/php:${PHP_VERSION}" AS ruby-build

ARG RUBY_INSTALL_VERSION=0.7.0
ARG RUBY_VERSION=2.6.1

ARG DEBIAN_FRONTEND=noninteractive
WORKDIR /home/circleci
ENV PATH=/home/circleci/.rubies/bin:$PATH

# Install ruby-install
# See: https://github.com/postmodern/ruby-install#install
RUN wget -O "ruby-install-${RUBY_INSTALL_VERSION}.tar.gz" "https://github.com/postmodern/ruby-install/archive/v${RUBY_INSTALL_VERSION}.tar.gz" && \
    tar -xzvf "ruby-install-${RUBY_INSTALL_VERSION}.tar.gz" && \
    cd "ruby-install-${RUBY_INSTALL_VERSION}" && \
    sudo make install

# Install ruby
RUN sudo apt-get -q update && \
    ruby-install --cleanup --install-dir /home/circleci/.rubies/ ruby $RUBY_VERSION && \
    sudo apt-get clean && sudo apt-get -y autoremove && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Update gem and bundler
RUN echo "gem: --no-document" > /home/circleci/.gemrc && \
    gem update --system && \
    gem update --force bundler

# Pre-install gems that take a long time to compile
RUN gem install nokogiri && \
    gem install websocket-driver

###################
# phantomjs-build #
###################

# Use the same php docker image as base to avoid surprises
FROM "circleci/php:${PHP_VERSION}" AS phantomjs-build

ARG PHANTOMJS_VERSION=2.1.1

ARG DEBIAN_FRONTEND=noninteractive
WORKDIR /home/circleci

RUN wget "https://bitbucket.org/ariya/phantomjs/downloads/phantomjs-${PHANTOMJS_VERSION}-linux-x86_64.tar.bz2" && \
    tar -jxvf "phantomjs-${PHANTOMJS_VERSION}-linux-x86_64.tar.bz2" && \
    sudo chmod +x "phantomjs-${PHANTOMJS_VERSION}-linux-x86_64/bin/phantomjs" && \
    sudo mv "phantomjs-${PHANTOMJS_VERSION}-linux-x86_64/bin/phantomjs" /usr/local/bin/phantomjs

# ################
# # wp-cli-build #
# ################

# # Use the same php docker image as base to avoid surprises
FROM "circleci/php:${PHP_VERSION}" AS wp-cli-build

ARG DEBIAN_FRONTEND=noninteractive
WORKDIR /home/circleci

# Download
RUN wget https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar && \
    wget https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar.asc && \
    wget https://raw.githubusercontent.com/wp-cli/builds/gh-pages/wp-cli.pgp

# Verify PGP signature
RUN gpg --dearmor wp-cli.pgp && \
    gpg --lock-never --no-default-keyring --keyring ./wp-cli.pgp.gpg --verify wp-cli.phar.asc wp-cli.phar

# Install
RUN sudo chmod +x wp-cli.phar && \
    sudo mv wp-cli.phar /usr/local/bin/wp

# ##########
# # actual #
# ##########

FROM "circleci/php:${PHP_VERSION}"

ARG RUBY_VERSION=2.6.1

ARG DEBIAN_FRONTEND=noninteractive
WORKDIR /home/circleci
ENV PATH=/home/circleci/.rubies/bin:/home/circleci/.composer/vendor/bin:$PATH

# Ruby
RUN sudo apt-get -q update && \
    sudo apt-get install -q -y --no-install-recommends \
    libyaml-dev && \
    sudo apt-get clean && sudo apt-get -y autoremove && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY --from=ruby-build /home/circleci/.rubies/ /home/circleci/.rubies/
COPY --from=ruby-build /home/circleci/.gem/ /home/circleci/.gem/
COPY --from=ruby-build /home/circleci/.gemrc /home/circleci/.gemrc

# PhantomJS
RUN sudo apt-get -q update && \
    sudo apt-get install -q -y --no-install-recommends \
    libfontconfig && \
    sudo apt-get clean && sudo apt-get -y autoremove && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY --from=phantomjs-build /usr/local/bin/phantomjs /usr/local/bin/phantomjs

# WP CLI
COPY --from=wp-cli-build /usr/local/bin/wp /usr/local/bin/wp

# MySQL
RUN sudo apt-get -q update && \
    sudo apt-get install -q -y --no-install-recommends \
    mariadb-client && \
    sudo apt-get clean && sudo apt-get -y autoremove && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    sudo docker-php-ext-install mysqli && sudo docker-php-ext-enable mysqli

# Composer
RUN sudo composer self-update

# Basic smoke test
RUN echo 'ruby --version' && ruby --version && \
    echo 'bundle --version' && bundle --version && \
    echo 'gem info nokogiri' && gem info nokogiri && \
    echo 'gem info websocket-driver' && gem info websocket-driver && \
    echo 'phantomjs --version' && phantomjs --version && \
    echo 'wp --info' && wp --info && \
    echo 'which mysql' && which mysql && \
    echo 'composer --version' && composer --version && \
    echo 'php --version' && php --version

# Define default command.
CMD ["bash"]
