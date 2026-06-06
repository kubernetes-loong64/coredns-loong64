# CoreDNS for LoongArch64

<p align="center"><a href="README.md">English</a> | <a href="README-zh.md">中文</a></p>

<p align="center"><img src="https://img.shields.io/badge/CoreDNS%20LoongArch64%20%E9%BE%99%E8%8A%AF%E6%9E%B6%E6%9E%84%E5%8F%91%E8%A1%8C%E7%89%88-blue?logo=docker&logoColor=white" alt="CoreDNS LoongArch64 龙芯架构发行版"></p>

Build [CoreDNS](https://github.com/coredns/coredns) binaries and Docker images for the **LoongArch64 (loong64)**
architecture via CI/CD.

## How it works

A GitHub Actions workflow clones the specified CoreDNS version, cross-compiles with
`GOOS=linux GOARCH=loong64 CGO_ENABLED=0`, and builds a Docker image using a loong64-compatible base image. Target
platform: `linux/loong64`.

See [Discussion #6 — Why Use container: debian:13?](https://github.com/orgs/kubernetes-loong64/discussions/6) for the
rationale behind the Debian 13 container choice.

## Branch naming

Push a branch named `loong64-<coredns-version>` (e.g. `loong64-v1.14.2`) to trigger a build. Append `+<build>`
(e.g. `loong64-v1.14.2+0`) to include build metadata.

## [Release](https://github.com/kubernetes-loong64/coredns-loong64/releases)

Push a tag matching `release-loong64-<coredns-version>` (e.g. `release-loong64-v1.14.2+0`) to publish
a GitHub Release with the built binaries and Docker images.

The `+<build>` suffix provides build metadata (e.g. `+0`, `+1-alpha.1`). It is stripped for the K8s-compatible
tag and replaced with `-` for the Docker tag.

The suffix in the build metadata indicates the release stage:

| Suffix  | Stage         |
|---------|---------------|
| `alpha` | Internal beta |
| `beta`  | Public beta   |
| `rc`    | Pre-release   |
| (none)  | Stable        |

## Release artifacts

Each release includes the following files:

| File                  | Description          |
|-----------------------|----------------------|
| `coredns`             | coredns binary       |
| `coredns-loong64.tar` | Docker image tarball |

Each file has a corresponding `.asc` detached GPG signature.

Docker images are pushed to:

- [![kubernetesloong64/coredns](https://img.shields.io/docker/v/kubernetesloong64/coredns?sort=semver&arch=loong64&logo=docker&label=kubernetesloong64%2Fcoredns)](https://hub.docker.com/r/kubernetesloong64/coredns/tags)
- [![kubernetesloong64/coredns-loong64](https://img.shields.io/docker/v/kubernetesloong64/coredns-loong64?sort=semver&arch=loong64&logo=docker&label=kubernetesloong64%2Fcoredns-loong64)](https://hub.docker.com/r/kubernetesloong64/coredns-loong64/tags)

| Image                                     | Description                            |
|-------------------------------------------|----------------------------------------|
| `kubernetesloong64/coredns:<tag>`         | K8s-compatible clean tag (stable only) |
| `kubernetesloong64/coredns:<tag>`         | Standard tag with metadata             |
| `kubernetesloong64/coredns-loong64:<tag>` | Tag with loong64 arch suffix           |

Example for `release-loong64-v1.14.2+0`:

```
kubernetesloong64/coredns:v1.14.2                   # K8s-compatible clean tag (stable only)
kubernetesloong64/coredns:v1.14.2-0                 # Standard tag with build metadata
kubernetesloong64/coredns-loong64:v1.14.2-0-loong64 # Tag with loong64 arch suffix
```

## Verifying releases

- Releases are signed with GPG.
- Download the public key from [keys.openpgp.org](https://keys.openpgp.org).
- Fingerprint: [FCF8724722CCBF9F51B1FBE376532BE7E3013105](https://keys.openpgp.org/debug?q=FCF8724722CCBF9F51B1FBE376532BE7E3013105)
- [Manual download](https://keys.openpgp.org/vks/v1/by-fingerprint/FCF8724722CCBF9F51B1FBE376532BE7E3013105)

```shell
gpg --keyserver keys.openpgp.org --recv-keys FCF8724722CCBF9F51B1FBE376532BE7E3013105
echo "FCF8724722CCBF9F51B1FBE376532BE7E3013105:6:" | gpg --import-ownertrust
```

Or download the key file manually and import it:

```shell
gpg --import /tmp/xxx
```

Each release artifact has a corresponding `.asc` detached signature. To verify, download both the file and its `.asc`
signature from the release, then:

```shell
gpg --verify <file>.asc <file>
```

## License

[Apache License 2.0](LICENSE)
