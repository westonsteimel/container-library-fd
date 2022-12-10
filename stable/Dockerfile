ARG VERSION="8.6.0"
ARG REVISION="d5bca085dd9f9091226bc01dc9b579979aad735f"

FROM ghcr.io/westonsteimel/rust-static-builder:stable as builder

ARG VERSION
ARG REVISION

RUN git clone --branch "v${VERSION}" https://github.com/sharkdp/fd /build/fd \
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

