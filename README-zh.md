# CoreDNS for LoongArch64

<p align="center"><a href="README.md">English</a> | <a href="README-zh.md">中文</a></p>

<p align="center"><img src="https://img.shields.io/badge/CoreDNS%20LoongArch64%20%E9%BE%99%E8%8A%AF%E6%9E%B6%E6%9E%84%E5%8F%91%E8%A1%8C%E7%89%88-blue?logo=docker&logoColor=white" alt="CoreDNS LoongArch64 龙芯架构发行版"></p>

通过 CI/CD 构建 [CoreDNS](https://github.com/coredns/coredns) 的 **LoongArch64 (loong64)** 架构二进制文件和 Docker 镜像。

## 工作原理

GitHub Actions 工作流克隆指定的 CoreDNS 版本，使用 `GOOS=linux GOARCH=loong64 CGO_ENABLED=0` 交叉编译，并使用兼容 loong64 的基础镜像构建 Docker 镜像。目标平台：`linux/loong64`。

关于 Debian 13 容器选型的理由，详见 [Discussion #6 — 为什么使用 container: debian:13？](https://github.com/orgs/kubernetes-loong64/discussions/6)。

## 分支命名

推送 `loong64/<coredns 版本>` 格式的分支（如 `loong64/v1.14.3`）即可触发构建。

## [发布](https://github.com/kubernetes-loong64/coredns-loong64/releases)

推送 `release-loong64/<coredns 版本>/<序号>` 格式的标签（如 `release-loong64/v1.14.3/1-alpha.1`）即可自动创建 GitHub Release 并上传构建产物和 Docker 镜像。

后缀表示发布阶段：

| 后缀      | 阶段   |
|---------|------|
| `alpha` | 内测版  |
| `beta`  | 公测版  |
| `rc`    | 预发布版 |
| （无后缀）   | 正式版  |

## 验证发布

发布文件使用 GPG 签名。从 [keys.openpgp.org](https://keys.openpgp.org) 下载公钥 [F3693AB74BBA0D84C227AB34F3A4B5061568FC57](https://keys.openpgp.org/debug?q=F3693AB74BBA0D84C227AB34F3A4B5061568FC57)：

```shell
gpg --keyserver keys.openpgp.org --recv-keys F3693AB74BBA0D84C227AB34F3A4B5061568FC57
echo "F3693AB74BBA0D84C227AB34F3A4B5061568FC57:6:" | gpg --import-ownertrust
```

每个发布包含一个 `signatures.tar.gz`，内含所有构建产物的分离签名。验证步骤：

```shell
# 从发布页面下载 signatures.tar.gz，然后：
tar -xzf signatures.tar.gz --strip-components=1
gpg --verify <文件>.asc <文件>
```

## 许可证

[Apache License 2.0](LICENSE)
