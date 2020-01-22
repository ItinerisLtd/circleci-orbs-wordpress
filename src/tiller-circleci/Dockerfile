#############################################################################################################
# GitHub: https://github.com/ItinerisLtd/circleci-orbs-wordpress/blob/master/src/tiller-circleci/Dockerfile #
#############################################################################################################

ARG NODE_VERSION=10

FROM "circleci/node:${NODE_VERSION}"

ARG DEBIAN_FRONTEND=noninteractive
WORKDIR /home/circleci

# For Trellis, specifically: ansible's synchronize module
RUN sudo apt-get -q update && \
    sudo apt-get install -q -y --no-install-recommends rsync && \
    sudo apt-get clean && sudo apt-get -y autoremove && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Python & Virtualenv
RUN sudo apt-get -q update && \
    # For Trellis, specifically: ansible
    sudo apt-get install -q -y python3-pip && \
    sudo apt-get clean && sudo apt-get -y autoremove && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    # For Trellis CLI, specifically: `trellis init`
    pip3 install --no-cache-dir virtualenv

# Trellis CLI
RUN curl -sL https://roots.io/trellis/cli/get | bash -s -- -d -b ~/bin

# TODO: Pre-install @itinerisltd/twoohoh

# # Basic smoke test
RUN echo 'node --version' && node --version && \
    echo 'npm --version' && npm --version && \
    echo 'yarn versions' && yarn versions && \
    echo 'python3 --version' && python3 --version && \
    echo 'pip3 --version' && pip3 --version && \
    echo 'pip3 check' && pip3 check && \
    echo 'virtualenv --version' && virtualenv --version && \
    echo 'rsync --version' && rsync --version && \
    echo 'trellis --version' && trellis --version

# Define default command.
CMD ["bash"]
