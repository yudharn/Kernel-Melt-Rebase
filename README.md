<div align="center">

# Melt Rebase (marble)

### Custom GKI Kernel for the Xiaomi POCO F5 / Redmi Note 12 Turbo

A rebase of **Melt Kernel** ([Pzqqt](https://github.com/Pzqqt)) for the **marble** platform, packing support for four root managers at once alongside modern kernel-level features: SuSFS, Re-Kernel, Baseband-guard, NoMount, and Droidspaces.

![Device](https://img.shields.io/badge/Device-marble-blue?style=flat-square)
![Kernel](https://img.shields.io/badge/Kernel-GKI_5.10-blue?style=flat-square)
![License](https://img.shields.io/badge/License-GPL--2.0-lightgrey?style=flat-square)

![KernelSU](https://img.shields.io/badge/KernelSU-supported-success?style=flat-square)
![KoWSU](https://img.shields.io/badge/KoWSU-supported-success?style=flat-square)
![KernelSU--Next](https://img.shields.io/badge/KernelSU--Next-supported-success?style=flat-square)
![ReSukiSU](https://img.shields.io/badge/ReSukiSU-supported-success?style=flat-square)
![SUSFS](https://img.shields.io/badge/SUSFS-integrated-success?style=flat-square)
![ROM](https://img.shields.io/badge/ROM-HyperOS%2FColorOS%2FOxygenOS-orange?style=flat-square)

</div>

---

## Table of Contents

- [About This Project](#about-this-project)
- [Device Specifications](#-device-specifications)
- [Key Features](#-key-features)
- [Supported Root Managers](#-supported-root-managers)
- [Build and Release Process](#-build-and-release-process)
- [Installation](#-installation)
- [Building from Source](#-building-from-source)
- [Disclaimer](#-disclaimer)
- [Credits and Acknowledgments](#-credits-and-acknowledgments)
- [License](#-license)

---

## About This Project

**Melt Rebase** is a custom kernel for marble (Xiaomi POCO F5 / Redmi Note 12 Turbo), built on top of the `melt-rebase` branch of **Melt Kernel** by [Pzqqt](https://github.com/Pzqqt) — the most widely known open-source kernel for this device.

Pzqqt deliberately leaves SUSFS integration and third-party root managers out of the official source. Melt Rebase fills that gap: it adds support for four root manager variants at once, kernel-level root hiding via SUSFS, critical-partition protection via Baseband-guard, a filesystem redirection layer via NoMount, Linux container support via Droidspaces, and freeze/thaw event reporting via Re-Kernel — all shipped automatically as ready-to-flash zips.

---

## 📱 Device Specifications

| Item | Detail |
|---|---|
| Device | Xiaomi POCO F5 / Redmi Note 12 Turbo |
| Codename | `marble` (`marblein` for the Poco F5 variant) |
| Chipset | Qualcomm Snapdragon 7 Gen 1 (SM7450) |
| Kernel base | Linux 5.10.x — GKI 2.0 (`android12-5.10`) |
| Base source | [Pzqqt/android_kernel_xiaomi_marble](https://github.com/Pzqqt/android_kernel_xiaomi_marble) @ branch `melt-rebase` |
| Toolchain | LLVM/Clang |
| Packaging | AnyKernel3 |
| ROM compatibility | HyperOS, ColorOS and OxygenOS |

---

## ✨ Key Features

#### 🔓 SuSFS
The `susfs4ksu` patch ([simonpunk](https://gitlab.com/simonpunk)) hides root traces at the kernel level — from the `su` binary and mount points to process names — so apps that check for root status or system integrity don't catch it.

#### 🌐 Re-Kernel — Netlink & eBPF
Reports background-process freeze/thaw events to userspace through two paths at once: a classic **Netlink** socket, and an **eBPF** daemon (`rekerneld`) that self-loads its own BPF program. Useful for preventing delayed or dropped notifications caused by aggressive freezers like MIUI/HyperOS.

#### 🛡️ Baseband-guard (BBG)
A lightweight LSM (Linux Security Module) from [vc-teahouse](https://github.com/vc-teahouse) that blocks unauthorized writes to critical partitions such as the baseband/modem and boot chain, cutting the risk of a lost IMEI or a brick caused by a rogue module or process — even one running as root.

#### 📂 NoMount
A VFS path-redirection framework from [maxsteeel](https://github.com/maxsteeel). Instead of the usual overlay mounting, NoMount injects or hides files by intercepting filesystem calls directly, complete with a per-UID filter — so banking apps or root scanners still see a clean, untouched filesystem.

#### 📦 Droidspaces
Kernel-level container namespace support from [ravindu644](https://github.com/ravindu644), letting you run full Linux distributions (systemd, OpenRC, etc.) directly on top of the Android kernel, each with its own process isolation, mount table, and cgroup hierarchy.

---

## 🧩 Supported Root Managers

| Root Manager | Base | Notes |
|---|---|---|
| **KernelSU** (Official) | [tiann/KernelSU](https://github.com/tiann/KernelSU) | The original KernelSU implementation. |
| **KernelSU-KoWSU** | [deepongi-labs](https://github.com/deepongi-labs/KernelSU-KoWSU) | Personal fork focused on LKM support. |
| **KernelSU-Next** | [KernelSU-Next team](https://github.com/KernelSU-Next) | Fork with broader kernel compatibility and App Profile. |
| **ReSukiSU** | [ReSukiSU team](https://github.com/ReSukiSU) | SukiSU Ultra derivative, focused on stability and multi-manager support. |

All four variants are built in parallel on every release — just grab the zip for whichever root manager you prefer from the **Releases** page.

> ⚠️ Don't install more than one root manager at a time on the same device.

---

## ⚙️ Build and Release Process

Every build runs automatically through **GitHub Actions** as a multi-manager matrix, with periodic syncs to upstream Melt Kernel. Each release's metadata also tracks the exact NoMount commit in use, so you always know precisely which patch version is in the build you download.

---

## 📦 Installation

1. Download the **AnyKernel3** zip for your chosen root manager from the **Releases** page.
2. **Back up** your stock boot,vendor boot image / partition before doing anything.
3. Flash the zip through a custom recovery (TWRP/OrangeFox)
4. Reboot the device.
5. Install the matching manager app (KernelSU Manager, KernelSU-Next Manager, etc.), then finish the root setup.

---

## 🛠️ Building from Source

1. Clone this repository on the `melt-rebase` branch.
2. Set up the LLVM/Clang toolchain along with a standard GKI kernel build environment.
3. Adjust the marble defconfig for your desired root manager.
4. Build the kernel image, then package the output with **AnyKernel3**.

For the full automated build flow (multi-manager matrix), check the workflow used to publish official builds directly in this repo's **Actions** tab / **Releases** page.

---

## ⚠️ Disclaimer

This is a custom kernel, **not official** from Xiaomi or Pzqqt. Install at your own risk — flashing a kernel always carries some risk of a bootloop. Always back up your stock boot, vendor boot image before trying it.

Please report issues related to SUSFS, NoMount, or any third-party root manager **in this repository**, not to upstream Melt Kernel or each feature's original project.

---

## 🙏 Credits and Acknowledgments

**Kernel Source**
- [Pzqqt](https://github.com/Pzqqt) — Melt Kernel, upstream source and ongoing maintenance for marble
- [Xiaomi/MIUI kernel source maintainers](https://github.com/MiCode) — base vendor kernel source

**Root Managers**
- [tiann](https://github.com/tiann) — KernelSU
- [deepongi-labs](https://github.com/deepongi-labs) — KernelSU-KoWSU
- [KernelSU-Next team](https://github.com/KernelSU-Next) — KernelSU-Next
- [ReSukiSU team](https://github.com/ReSukiSU) — ReSukiSU

**Kernel Features**
- [simonpunk](https://gitlab.com/simonpunk) — `susfs4ksu` patch (SUSFS)
- [Sakion-Team](https://github.com/Sakion-Team) — Re-Kernel (Netlink & eBPF)
- [vc-teahouse](https://github.com/vc-teahouse) — Baseband-guard
- [maxsteeel](https://github.com/maxsteeel) — NoMount
- [ravindu644](https://github.com/ravindu644) — Droidspaces

**Tooling & Reference**
- [osm0sis](https://github.com/osm0sis) — AnyKernel3
- [WildKernels](https://github.com/WildKernels) — reference CI/CD and release patterns

---

## 📄 License

This project is a derivative of the Linux kernel and follows the **GPL-2.0** license, in line with the upstream source's license and every component used within it.

<div align="center">

Built and maintained by **Yudharn**

</div>
