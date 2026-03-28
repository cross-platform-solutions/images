FROM moby/buildkit:rootless

USER root

RUN apk add --no-cache \
    bash \
    ca-certificates \
    coreutils \
    git \
    openssh-client

RUN mkdir -p /home/runner/.docker \
 && chown -R 1000:1000 /home/runner

USER 1000:1000

ENV HOME=/home/runner

ENTRYPOINT []

CMD ["sh"]
