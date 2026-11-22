# n1-istoreos

为斐讯 N1（Phicomm N1, Amlogic S905D）自构建的 iStoreOS 固件。

## 与官方版的关系

- 基线与官方 alpha 线的 N1 构建完全一致：源码 `istoreos/istoreos` 分支
  `istoreos-25.12`，锁定官方 2026-11-06 构建（25.12.6-2026110611）所用的
  源码提交 `111a980544`；feeds 按官方 `feeds.buildinfo` 逐个锁定版本还原；
  配置以官方发布的 `config.seed` 为底。
- **唯一改动**：在配置里多选一项 `CONFIG_PACKAGE_kmod-nft-queue=y`。

原因：实测官方 25.12.6-2026110611 的 N1 固件缺 `nft_queue.ko`，
Open-Box 的六项依赖（tun / nft-queue / nft-nat / veth / ip-full / ca-bundle）
只齐五项，且固件预置软件源里没有 kmod 源，装机后无法补装。
本仓库构建的固件六项齐全，其余一切与官方一致（默认管理地址
192.168.100.1、root 默认密码 `password` 等均不变）。
