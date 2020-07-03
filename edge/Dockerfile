ARG GIT_TAG="master"
ARG FD_VERSION="edge"

FROM westonsteimel/rust-static-builder:nightly as builder

ARG GIT_TAG

RUN git clone --depth 1 --branch "${GIT_TAG}" https://github.com/sharkdp/fd /build/fd \
    && cd /build/fd \
    && /home/builder/build.sh

FROM alpine:edge as config

RUN addgroup fd \
    && adduser -G fd -s /bin/sh -D fd

FROM scratch

ARG FD_VERSION

COPY --from=config /etc/passwd /etc/passwd
COPY --from=builder /build/fd/target/x86_64-unknown-linux-musl/release/fd /usr/local/bin/fd

USER fd

ENV TERM xterm-256color

ENTRYPOINT ["/usr/local/bin/fd"]
CMD ["--help"]

LABEL org.opencontainers.image.title="fd" \
    org.opencontainers.image.description="fd in Docker" \
    org.opencontainers.image.version="${FD_VERSION}" \
    version="${FD_VERSION}"

