FROM moby/buildkit:latest

USER root

RUN apk add --no-cache \
    bash \
    ca-certificates \
    coreutils \
    git \
    openssh-client

RUN mkdir -p /root/.docker

ENV HOME=/root

ENTRYPOINT []

CMD ["sh"]
