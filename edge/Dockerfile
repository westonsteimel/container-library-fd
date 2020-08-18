ARG SOURCE
ARG REVISION
ARG CREATED
ARG VERSION
ARG GIT_REF="${VERSION}"

FROM westonsteimel/rust-static-builder:nightly as builder

ARG GIT_REF

RUN git clone --depth 1 --branch "${GIT_REF}" https://github.com/sharkdp/fd /build/fd \
    && cd /build/fd \
    && /home/builder/build.sh

FROM alpine:edge as config

RUN addgroup fd \
    && adduser -G fd -s /bin/sh -D fd

FROM scratch

ARG SOURCE
ARG REVISION
ARG CREATED
ARG VERSION

COPY --from=config /etc/passwd /etc/passwd
COPY --from=builder /build/fd/target/x86_64-unknown-linux-musl/release/fd /usr/local/bin/fd

USER fd

ENV TERM xterm-256color

ENTRYPOINT ["/usr/local/bin/fd"]
CMD ["--help"]

LABEL org.opencontainers.image.title="fd" \
    org.opencontainers.image.description="fd in Docker" \
    org.opencontainers.image.source="${SOURCE}" \
    org.opencontainers.image.revision="${REVISION}" \
    org.opencontainers.image.created="${CREATED}" \
    org.opencontainers.image.version="${VERSION}"

