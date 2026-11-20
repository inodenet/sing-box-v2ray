# sing-box with V2Ray API

This repository builds the upstream [SagerNet/sing-box](https://github.com/SagerNet/sing-box) source with the additional `with_v2ray_api` build tag.

## Supported release artifacts

For each stable upstream release, the workflow builds:

- `linux/amd64`
- `linux/arm64`

Each release contains:

- `sing-box-X.Y.Z-linux-amd64.tar.gz`
- `sing-box-X.Y.Z-linux-arm64.tar.gz`
- Per-archive SHA-256 files
- `checksums.sha256`

Each archive contains the sing-box executable, the upstream `libcronet.so` runtime, the upstream license, the selected build tags, and `SBP_BUILD_INFO` with the exact upstream source commit.

## Build behavior

The GitHub Actions workflow:

1. Checks the latest stable SagerNet/sing-box release once per day.
2. Verifies the upstream release and matching Git tag.
3. Checks out the exact upstream tag.
4. Uses the upstream default build tags, adding `with_musl` and `with_v2ray_api`.
5. Uses the Cronet revision pinned by that upstream tag.
6. Builds both Linux architectures and checks that the V2Ray StatsService is present.
7. Starts an AMD64 test instance and exercises the gRPC `GetSysStats` and `QueryStats` methods.
8. Publishes versioned archives and SHA-256 checksums as a GitHub Release.

A version can also be selected manually from **Actions → Build sing-box with V2Ray API → Run workflow**. Leave the version field empty to use the latest stable upstream release.

Existing version releases are not rebuilt or overwritten automatically.

## Important runtime note

The upstream default build tags include the Naive outbound. Its Cronet shared library is therefore included in each archive. Keep `libcronet.so` alongside the executable when installing the archive. The application integrating this binary must also make the library discoverable by the dynamic loader when the Naive outbound is used.

The current workflow verifies that the V2Ray API is exposed and that its gRPC methods respond. It does **not** yet perform a full inbound traffic test proving per-user uplink/downlink counters for Hysteria2 and Shadowsocks. That should be added as a separate end-to-end test before relying on those counters for billing.

## License

The distributed sing-box binary and its included upstream components remain subject to their respective upstream licenses. Refer to the license files and source repositories for the applicable terms.
