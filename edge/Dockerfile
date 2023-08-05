ARG VERSION="master"
ARG REVISION="9ce43b2d7b1ae80c53e8801d2ce4c0499e88d78f"

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

