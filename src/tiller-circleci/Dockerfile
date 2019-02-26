###########################################################################################################
# GitHub: https://github.com/ItinerisLtd/circleci-orbs-roots/blob/master/src/tiller-circleci/Dockerfile #
###########################################################################################################

ARG NODE_VERSION=10

FROM "circleci/node:${NODE_VERSION}"

ARG DEBIAN_FRONTEND=noninteractive

## Ansible
RUN echo 'deb http://ppa.launchpad.net/ansible/ansible/ubuntu trusty main' | sudo tee -a /etc/apt/sources.list && \
    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367 && \
    sudo apt-get update && \
    sudo apt-get install -q -y --no-install-recommends ansible rsync && \
    sudo apt-get clean && sudo apt-get -y autoremove && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

## Sage
RUN sudo apt-get -q update && \
    sudo apt-get install -q -y --no-install-recommends libpng-dev libpng16-16 && \
    sudo apt-get clean && sudo apt-get -y autoremove && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Basic smoke test
RUN echo 'ansible --version' && ansible --version && \
    echo 'rsync --version' && rsync --version && \
    echo 'node --version' && node --version && \
    echo 'npm --version' && npm --version && \
    echo 'yarn versions' && yarn versions

# Define default command.
CMD ["bash"]
