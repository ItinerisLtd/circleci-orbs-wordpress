#############################################################################################################
# GitHub: https://github.com/ItinerisLtd/circleci-orbs-wordpress/blob/master/src/tiller-circleci/Dockerfile #
#############################################################################################################

ARG NODE_VERSION=10

FROM "circleci/node:${NODE_VERSION}-browsers"

ARG ANSIBLE_VERSION=latest

ARG DEBIAN_FRONTEND=noninteractive
WORKDIR /home/circleci

## Python & Ansible
RUN sudo apt-get -q update && \
    sudo apt-get install -q -y --no-install-recommends "python3-setuptools" "python3-yaml" && \
    sudo "easy_install3" pip && \
    if [ "$ANSIBLE_VERSION" = "latest" ]; then export ANSIBLE_VERSION_CONSTRAINT=''; fi && \
    if [ "$ANSIBLE_VERSION" != "latest" ]; then export ANSIBLE_VERSION_CONSTRAINT="==${ANSIBLE_VERSION}"; fi && \
    sudo pip install "ansible${ANSIBLE_VERSION_CONSTRAINT}" && \
    sudo rm -rf ~/.cache/pip && \
    sudo apt-get clean && sudo apt-get -y autoremove && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

## rsync
RUN sudo apt-get -q update && \
    sudo apt-get install -q -y --no-install-recommends rsync && \
    sudo apt-get clean && sudo apt-get -y autoremove && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

## Sage
RUN sudo apt-get -q update && \
    sudo apt-get install -q -y --no-install-recommends libpng-dev libpng16-16 && \
    sudo apt-get clean && sudo apt-get -y autoremove && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Basic smoke test
RUN echo 'node --version' && node --version && \
    echo 'npm --version' && npm --version && \
    echo 'yarn versions' && yarn versions && \
    echo 'pip --version' && pip --version && \
    echo 'pip check' && pip check && \
    echo 'ansible --version' && ansible --version && \
    echo 'rsync --version' && rsync --version

# Define default command.
CMD ["bash"]
