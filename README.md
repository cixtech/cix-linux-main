# Linux patches from CIX for mainline

This repository provides a patch set for the mainline version of the Linux kernel.

# Overview
| Kernel version | Patch dir | Kernel config | Comments |
| --- | --- | --- | --- |
| v6.18.x | patches-6.18 | config/config-6.18.defconfig | This will track the latest LTS version of kernel |
| v7.0 | patches-7.0 | config/config-7.0.defconfig | This will track the latest tag of linux mainline |

# Hardware/Firmware requirements
- Orion O6 is tested with [edk2 firmware 1.2.1](https://github.com/radxa-pkg/edk2-cix/releases/tag/1.2.1)
- There are two necessary commits if you are using edk2 source code based on 2025Q4 release from other vendor: [Add reg-io-width property for SCMI shmem](https://github.com/radxa/edk2-platforms/commit/e131517238e068560adaffefff4b8646cce616fe), [Platform/Radxa: add SCMI toggle](https://github.com/radxa/edk2-platforms/commit/a35d5083e8f20616538539c608e5a0db340e3b8b)
- `Device Manager -> O/S Hardware Description Selection` has to be set to `ACPI`
- The following option has to be enabled in UEFI: `Device Manager -> Platform Configuration -> Compliance Configuratuin -> Enable ACPI SCMI`

# Support status
| hardware components | Status | Notes |
| --- | --- | --- |
| Typec DP Display | DP alt mode works | USB C port near the power button has issues when rendering videos decoded by vpu via GL(Tested with clapper playing fullscreen video). |
| DP Display | Supported | DP has issues when rendering videos decoded by vpu via GL(Tested with clapper playing fullscreen video). |
| HDMI Display | Supported | HDMI has issues when rendering videos decoded by vpu via GL(Tested with clapper playing fullscreen video). |
| DP Sound | TODO |
| Headphone jack | Supported |
| GPU | Supported |
| VPU | Supported via [external DKMS](https://github.com/cixtech/cix_opensource__vpu_driver/tree/cix_mainline_dev) |
| NPU | Supported via [external DKMS](https://github.com/cixtech/cix_opensource__npu_driver/tree/cix_mainline_dev) |
| USB | Supported |
| PCIE | Works without patches |

# Build kernel
This is an example of building kernel with patches and kernel config of v7.0 on a native arm64 machine.
```
# Clone this repo
git clone https://github.com/cixtech/cix-linux-main

# Clone kernel source code
git clone --depth 1 https://github.com/torvalds/linux.git -b v7.0-rc4

# Get into source dir
cd linux

# Create a branch for development
git branch dev-7.0 && git checkout dev-7.0

# Apply patches
git am ../cix-linux-main/patches-7.0/*.patch

# Copy kernel config
cp ../cix-linux-main/config/config-7.0.defconfig .config

# Make oldconfig
make olddefconfig

# Build kernel deb
make -j$(nproc) bindeb-pkg
```

# Kernel parameters
There are following kernel parameters which are necessary for booting kernel:
- clk_ignore_unused

# Kernel firmware
| Kernel driver | firmware file |
| --- | --- |
| panthor | firmware of gpu is available in [upstream linux-firmware repo since 20250808](https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/commit/?id=930ef9046e3848df688d98b7d5e68154b031dc66) |
| dsp remoteproc | `dsp_fw.bin` is available at [github repo of cixtech](https://github.com/cixtech/cix_proprietary__cix_proprietary/raw/refs/heads/cix_p1_k6.6_master/cix_proprietary-debs/cix-audio-dsp/usr/lib/firmware/dsp_fw.bin)

# Userspace driver
| kernel space driver | userspace driver |
| --- | --- |
| panthor | mesa since v25.1 supported by [upstream MR](https://gitlab.freedesktop.org/mesa/mesa/-/merge_requests/34032) |