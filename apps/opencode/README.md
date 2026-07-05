# opencode

The [SST opencode](https://github.com/sst/opencode) CLI packaged as a pinned,
signed image — the prebuilt (bun-compiled) glibc linux binary fetched from the
upstream GitHub release onto a slim glibc base.

- **Image:** `ghcr.io/nicolerenee/opencode:<version>`
- **Consume:** either run it directly, or `COPY --from` the binary into another
  image, pinned by digest:

  ```dockerfile
  COPY --from=ghcr.io/nicolerenee/opencode:1.17.13-1@sha256:<digest> \
       /usr/local/bin/opencode /usr/local/bin/opencode
  ```

## Versioning

`version` is `<upstream opencode version>-<image revision>`. The upstream version
is pinned in the `Dockerfile` (`OPENCODE_VERSION`) and mirrored in
`metadata.json`; bump both together. Set `OPENCODE_SHA256_{AMD64,ARM64}` to the
per-arch sha256 of `opencode-linux-<arch>.tar.gz` for the pinned version so the
build verifies the download.
