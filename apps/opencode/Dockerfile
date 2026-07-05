# opencode — the SST opencode CLI as a standalone, pinned image. Distributed as
# a prebuilt (bun-compiled) glibc binary from GitHub releases; this image just
# fetches + verifies it onto a slim glibc base so downstreams can `COPY --from`
# the binary into their own image or run it directly.
#
# Built by this repo's Release pipeline. Build context is this app dir; the
# upstream version is pinned here and mirrored in metadata.json's `version`
# (<upstream>-<revision>). Bump both together.
FROM debian:bookworm-slim

ARG TARGETARCH
ARG OPENCODE_VERSION=1.17.13
# Per-arch sha256 of opencode-linux-<arch>.tar.gz for OPENCODE_VERSION. Fill in
# and keep in lockstep with the version bump; the build fails closed if a digest
# is set and doesn't match.
ARG OPENCODE_SHA256_AMD64=157afa289d1a8d9372de0ce19ac726119b937a1f6b201808d46f06e4e59bb348
ARG OPENCODE_SHA256_ARM64=bbaccdd374aaab66cd97c7f8ad1c080aa393610fa5f80ee8dfc007f9500afaf9

RUN set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends curl ca-certificates; \
    rm -rf /var/lib/apt/lists/*; \
    case "${TARGETARCH}" in \
      amd64) ASSET_ARCH=x64;   SHA="${OPENCODE_SHA256_AMD64}" ;; \
      arm64) ASSET_ARCH=arm64; SHA="${OPENCODE_SHA256_ARM64}" ;; \
      *) echo "unsupported TARGETARCH ${TARGETARCH}"; exit 1 ;; \
    esac; \
    url="https://github.com/sst/opencode/releases/download/v${OPENCODE_VERSION}/opencode-linux-${ASSET_ARCH}.tar.gz"; \
    curl -fsSL -o /tmp/opencode.tar.gz "$url"; \
    if [ -n "$SHA" ]; then echo "${SHA}  /tmp/opencode.tar.gz" | sha256sum -c -; fi; \
    tar -xzf /tmp/opencode.tar.gz -C /usr/local/bin opencode; \
    rm /tmp/opencode.tar.gz; \
    chmod 0755 /usr/local/bin/opencode; \
    /usr/local/bin/opencode --version

# Non-root by default (downstreams that COPY the binary set their own user).
RUN useradd --system --uid 65532 --no-create-home --shell /usr/sbin/nologin opencode
USER 65532
ENTRYPOINT ["/usr/local/bin/opencode"]
