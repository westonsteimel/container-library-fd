ARG VERSION="master"
ARG REVISION="3b2fd158b5ef4ba5f8cccddc2d2efb1de24b6c47"

FROM ghcr.io/westonsteimel/rust-static-builder:stable as builder

ARG VERSION
ARG REVISION

RUN git clone --branch "${VERSION}" https://github.com/sharkdp/fd /build/fd \
    && cd /build/fd \
    && git reset --hard "${REVISION}" \
    && NOCOMPRESS=1 /home/builder/build.sh

FROM alpine:latest as config

RUN addgroup fd \
    && adduser -G fd -s /bin/sh -D fd

FROM scratch

ARG VERSION
ARG REVISION
ENV FD_VERSION="${VERSION}" \
    FD_REVISION="${REVISION}" \
    TERM="xterm-256color"

COPY --from=config /etc/passwd /etc/passwd
COPY --from=builder /build/artifacts/fd /usr/local/bin/fd

USER fd

ENTRYPOINT ["/usr/local/bin/fd"]
CMD ["--help"]

LABEL org.opencontainers.image.title="fd" \
    org.opencontainers.image.description="fd in Docker" \
    org.opencontainers.image.revision="${REVISION}" \
    org.opencontainers.image.version="${VERSION}"

