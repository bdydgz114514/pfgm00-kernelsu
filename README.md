# PFGM00 (OPPO A93s 5G) KernelSU 云编译

设备：OPPO PFGM00 / MT6833 / Android 12 / kernel 4.14.186 / A-only / 动态分区。

本仓库用 GitHub Actions 编译带 KernelSU 的 4.14 内核：

- 内核源码：`Asgore-Dreemur/kernel_oppo_mt6833`（`PFGM00_defconfig`，4.14.186）
- KernelSU：`tiann/KernelSU`（`kernel/setup.sh` 集成到 `drivers/kernelsu`）
- 工具链：LineageOS 的 AOSP clang `r416183b`

## 触发

Actions → build-kernelsu → Run workflow，或 push 到 main。

产物：`pfgm00-kernelsu-artifacts`

- `Image` / `Image.gz`（新内核）
- `dtb` / `*.dtb` / `dtbo.img`（如有）
- `build.log`、`defconfig.log`

## 拿到产物后

1. 用 mtkclient 从手机 dump 原厂 `boot.img`（BROM 模式）；
2. `magiskboot unpack boot.img`，把 `kernel` 替换成产物 `Image`（或 `Image.gz`，看原 boot 的格式）；
3. `magiskboot repack boot.img new-boot.img`；
4. 解锁 BL 后，fastbootd 或 BROM 刷入 `new-boot.img`。

> 如果 Action 失败，先看 `build.log` 末尾；常见问题是 KernelSU 版本对 4.14 的兼容、clang 版本、MTK vendor 头文件缺失。

## 注意

- 这是 4.14 非 GKI 内核，KernelSU 必须编进内核，不能用 LKM 模式。
- 解锁前请先按 `mtk/README_强解.md` 的流程备份 boot/vbmeta/seccfg/preloader。
- 刷入前保留原厂 boot.img，随时可回滚。
