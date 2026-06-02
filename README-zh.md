# CoreDNS for LoongArch64

<p align="center"><a href="README.md">English</a> | <a href="README-zh.md">中文</a></p>

<p align="center"><img src="https://img.shields.io/badge/CoreDNS%20LoongArch64%20%E9%BE%99%E8%8A%AF%E6%9E%B6%E6%9E%84%E5%8F%91%E8%A1%8C%E7%89%88-blue?logo=docker&logoColor=white" alt="CoreDNS LoongArch64 龙芯架构发行版"></p>

通过 CI/CD 构建 [CoreDNS](https://github.com/coredns/coredns) 的 **LoongArch64 (loong64)** 架构二进制文件和 Docker 镜像。

## 工作原理

GitHub Actions 工作流克隆指定的 CoreDNS 版本，使用 `GOOS=linux GOARCH=loong64 CGO_ENABLED=0` 交叉编译，并使用兼容 loong64
的基础镜像构建 Docker 镜像。目标平台：`linux/loong64`。

关于 Debian 13
容器选型的理由，详见 [Discussion #6 — 为什么使用 container: debian:13？](https://github.com/orgs/kubernetes-loong64/discussions/6)。

## 分支命名

推送 `loong64-<coredns 版本>` 格式的分支（如 `loong64-v1.14.2`）即可触发构建。可追加 `+<build>`
（如 `loong64-v1.14.2+0`）携带构建元数据。

## [发布](https://github.com/kubernetes-loong64/coredns-loong64/releases)

推送 `release-loong64-<coredns 版本>` 格式的标签（如 `release-loong64-v1.14.2+0`）即可自动创建 GitHub
Release 并上传构建产物和 Docker 镜像。

`+<build>` 后缀提供构建元数据（如 `+0`、`+1-alpha.1`）。在 K8s 兼容标签中会被去除，在 Docker 标签中 `+` 会替换为 `-`。

后缀表示发布阶段：

| 后缀      | 阶段   |
|---------|------|
| `alpha` | 内测版  |
| `beta`  | 公测版  |
| `rc`    | 预发布版 |
| （无后缀）   | 正式版  |

## 发布产物

每个发布包含以下文件：

| 文件                    | 描述            |
|-----------------------|---------------|
| `coredns`             | coredns 二进制文件 |
| `coredns-loong64.tar` | Docker 镜像压缩包  |

每个文件都有对应的 `.asc` 分离 GPG 签名。

Docker 镜像推送至：

- [![kubernetesloong64/coredns](https://img.shields.io/docker/v/kubernetesloong64/coredns?sort=semver&arch=loong64&logo=docker&label=kubernetesloong64%2Fcoredns)](https://hub.docker.com/r/kubernetesloong64/coredns/tags)
- [![kubernetesloong64/coredns-loong64](https://img.shields.io/docker/v/kubernetesloong64/coredns-loong64?sort=semver&arch=loong64&logo=docker&label=kubernetesloong64%2Fcoredns-loong64)](https://hub.docker.com/r/kubernetesloong64/coredns-loong64/tags)

| 镜像                                        | 描述                |
|-------------------------------------------|-------------------|
| `kubernetesloong64/coredns:<tag>`         | K8s 兼容干净标签（仅正式版）  |
| `kubernetesloong64/coredns:<tag>`         | 带元数据的标准标签         |
| `kubernetesloong64/coredns-loong64:<tag>` | 含 loong64 架构后缀的标签 |

`release-loong64-v1.14.2+0` 发布示例：

```
kubernetesloong64/coredns:v1.14.2              # K8s 兼容干净标签（仅正式版）
kubernetesloong64/coredns:v1.14.2-0            # 带构建元数据的标准标签
kubernetesloong64/coredns-loong64:v1.14.2-0-loong64  # 含 loong64 架构后缀的标签
```

## 验证发布

- 发布文件使用 GPG 签名。
- 从 [keys.openpgp.org](https://keys.openpgp.org) 下载公钥。
- [FCF8724722CCBF9F51B1FBE376532BE7E3013105](https://keys.openpgp.org/debug?q=FCF8724722CCBF9F51B1FBE376532BE7E3013105)

```shell
gpg --keyserver keys.openpgp.org --recv-keys FCF8724722CCBF9F51B1FBE376532BE7E3013105
echo "FCF8724722CCBF9F51B1FBE376532BE7E3013105:6:" | gpg --import-ownertrust
```

每个发布产物都有对应的 `.asc` 分离签名。验证时，从发布页面下载文件和对应的 `.asc` 签名文件，然后：

```shell
gpg --verify <文件>.asc <文件>
```

## 许可证

[Apache License 2.0](LICENSE)
