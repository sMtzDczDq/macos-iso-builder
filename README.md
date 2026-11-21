# Overview

Build bootable macOS installer ISOs and DMGs directly from Apple's servers — no Mac required.

This project has two parts:
1. A script (`mkmaciso`) that uses only macOS built-in tools and commands to download and install the full macOS installer from Apple's servers into **/Applications**, and then creates bootable ISO/DMG images.
2. GitHub Action workflows that run `mkmaciso` on Azure datacenter-hosted Mac minis if you don't have macOS.

## Before you start

Check [Release page](https://github.com/LongQT-sea/mkmaciso/releases/latest) first - someone might've already built what you need. If not, see [How to use](#how-to-use) to build your own.

> [!Important]
> GitHub-hosted runners are a free public resource — please use them responsibly.

## Disk Image Formats

| | IMG (raw) | ISO |
|---|---|---|
| Best for | QEMU/Proxmox/OpenCore VMs, bootable USB | Virtual DVD/CD for VMs that support it |
| VM Support | Attach as virtual hard disk (AHCI/SATA/USB) | Attach as virtual DVD/CD drive |
| Layout | Raw GPT disk image | Hybrid UDF/HFS |

**IMG files (recommended for QEMU/OpenCore)** - Raw GPT disk images. Attach them as a virtual hard disk in QEMU/Proxmox/OpenCore setups (not as a CD/DVD). They can also be flashed to a USB drive with [Rufus](https://rufus.ie/en/#download) *(Windows)* or `dd` *(Linux/macOS)*. For VMware/VirtualBox, convert with `qemu-img` if required.

**ISO files** - Hybrid UDF/HFS images. Attach them as a virtual DVD/CD drive. Some OpenCore/QEMU setups may not boot these as CD media; if that happens, use the IMG format instead. They can be mounted in Windows/macOS for inspection.

## Supported versions

Pretty much everything from OS X Lion (10.7, 2011) through the latest macOS Tahoe (26, 2025). Full list:

Lion, Mountain Lion, Mavericks, Yosemite, El Capitan, Sierra, High Sierra, Mojave, Catalina, Big Sur, Monterey, Ventura, Sonoma, Sequoia, Tahoe.

---

## How to use

### Don't have macOS? Use GitHub Actions

> [!TIP]
> <details>
> <summary>Click here to watch a visual guide (GIF)</summary>
>
> ![How to fork and run workflow](https://raw.githubusercontent.com/LongQT-sea/macos-iso-builder/main/.github/how_to_fork_and_run_workflow.gif)
>
> </details>

1. Star and then [Fork](https://github.com/LongQT-sea/macos-iso-builder/fork) this repository (requires a GitHub account).<br>
   <img src="https://raw.githubusercontent.com/LongQT-sea/macos-iso-builder/main/.github/star_and_fork.jpg" width="500">
2. Navigate to the **Actions** tab in your forked repository.
3. Click the green **"I understand my workflows, go ahead and enable them"** button.
4. Select a workflow from the left sidebar:
   * **Recovery ISO** *(recommended)* - Small recovery image, builds in 2-5 minutes. Good for VMs.
   * **Full Installer** - Complete offline installer, 5-18GB, takes 5-60 minutes to build.
5. Click the **"Run workflow"** button and configure the workflow inputs:

   * **macOS version** – Choose a version (*Sequoia*, *Sonoma*, etc.).
   * **Image format** – Choose `img` for QEMU/Proxmox/OpenCore VMs or USB drives, or `iso` for a virtual DVD/CD image.
6. Click the green **"Run workflow"** button to start the build, then wait for the workflow to complete.
7. Once completed, reload the page and scroll down to the **Artifacts** section. Click the artifact link to start downloading (e.g., `macOS_Sequoia_15.7.4.iso`).
8. **Recovery ISO** artifacts are zipped — unzip before use.

---

### Already have macOS? Run `mkmaciso` locally

Quick run using Terminal.app (change `tahoe` to whatever you want):
```bash
curl -s https://raw.githubusercontent.com/LongQT-sea/mkmaciso/main/mkmaciso | bash -s tahoe
```

Or download the script first, then run with parameters:
```bash
curl -O https://raw.githubusercontent.com/LongQT-sea/mkmaciso/main/mkmaciso
chmod +x mkmaciso
./mkmaciso --help
```

Running `./mkmaciso` without arguments gives you an interactive menu.

---

## Tips

For QEMU/OpenCore VMs, attach the **IMG** as a virtual hard disk (AHCI/SATA), not as a CD/DVD. For VMs that prefer DVD media, you can use the ISO format. Proxmox users — if you want better performance, look into GPU passthrough. I have another repo ([OpenCore-ISO](https://github.com/LongQT-sea/OpenCore-ISO)) that might help with installation, and one for [Intel iGPU passthrough](https://github.com/LongQT-sea/intel-igpu-passthru) specifically.

For bootable USB drives, flash the IMG with `dd` or Rufus. After flashing there may be leftover space on the drive; you can use that to create a FAT32 partition for your EFI folder if you need one.

If you're using `dd` on Linux, triple-check your target device. `dd` doesn't ask for confirmation.

## Requirements for mkmaciso

- macOS 10.9 or newer (11+ is better)
- Intel Mac recommended, Apple Silicon works but with some limitations
- 20-40GB of free space while building
- Internet connection
- sudo access

## Credits

Apple for macOS and their update servers, [Mavericks Forever](https://mavericksforever.com/) for documenting the Mavericks recovery protocol, and the [InsanelyMac community](https://www.insanelymac.com/forum/topic/338810-create-legit-copy-of-macos-from-apple-catalog/) for their research on downloading macOS directly from Apple's catalog.

## Legal stuff

This tool downloads macOS images directly from Apple's official servers. Users are responsible for complying with [Apple's Software License Agreement](https://www.apple.com/legal/sla/). macOS is a trademark of Apple Inc.

Licensed under GPLv3.
