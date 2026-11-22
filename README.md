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

## 构建

GitHub Actions 手动触发：仓库 Actions 页 → “Build iStoreOS for N1” →
Run workflow。构建前有一步依赖自检，六项依赖有任何一项没选进配置，
流水线会直接失败而不是编出一份缺依赖的固件。

产物（Artifacts，保留 30 天）：

- `istoreos-*-n1-*.img.gz`：可刷镜像
- `full.config` / `config.buildinfo`：本次构建的实际生效配置

## 刷入

1. 用 balenaEtcher 或 Rufus（dd 模式）把 `.img.gz` 解压后的 `.img` 写进 U 盘
2. N1 插 U 盘启动，浏览器打开 192.168.100.1，root / `password` 登录，
   **第一时间改密码**
3. 装入 eMMC（覆盖盒内现有系统，先备份）：在 U 盘系统里整盘拷贝，
   `dd if=/dev/sda of=/dev/mmcblk1 bs=1M` 后关机拔 U 盘再开机
   （设备名以实际 `lsblk` 为准）
4. 装 Open-Box：
   `curl -fsSL https://raw.githubusercontent.com/liandu2024/Open-Box/main/scripts/install.sh | sh`
   面板默认端口 3036
