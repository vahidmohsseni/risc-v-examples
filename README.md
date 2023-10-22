# Goals
This repository aims to provide a set of instrucions to get a working Kubernetes cluster on the RISCV64 architecture through K3s. K3s is a K8s distribution, trying to be production ready and easy to install. It is especially suitable for Edge devices. See more : [K3s github repo](https://github.com/k3s-io/k3s#k3s---lightweight-kubernetes)

Pre-built binaries can be found in the v1.0 release. Instructions to build everything from source can be found in this repository. Build artifacts can be found in the v0.1 release.

# Get a working K3s cluster on a RISCV64 system

Follow the [Install from zero guide](https://github.com/chefmtt/riscv64/blob/main/install/deploy-k3s-from-zero.md) to launch a RISCV64 system running Ubuntu 20.04 in a virtual machine powered by QEMU, install K3s and its dependencies and start a K3s cluster. If you intend to simply follow the guide, the rest of this README is not important.

The guide targets a QEMU VM, but the binaries should work without any issue on a real system.

The guide targets Ubuntu 20.04. Other Linux distribution supports the RISCV64 architecture : Fedora, Debian, Arch, Gentoo, openSUSE...  
  
See :  
[Ubuntu](https://wiki.ubuntu.com/RISC-V)  
[RISC-V Days 01/06/2022 presentation on the state of Linux distros on RISC-V](https://riscv.or.jp/wp-content/uploads/Linux_Distros_on_RISC-V_status_update_RISC-V-Days_Tokyo_2022_Spring_day2_08_redhat_c.pdf)  
[Fedora](https://fedoraproject.org/wiki/Architectures/RISC-V/Installing)  
[openSUSE](https://en.opensuse.org/openSUSE:RISC-V)  
[Debian wiki](https://wiki.debian.org/RISC-V)  
[Debian image builder](https://gitlab.com/giomasce/dqib/blob/master/README.md)  
[Gentoo](https://wiki.gentoo.org/wiki/Project:RISC-V)

## Warning (if you want to use another image)

The Memory Management Unit (MMU) of RISC-V CPUs can have different virtualization schemes. See sections 4.3 to 4.6 of [the RISC-V Instruction Set Manual, Volume II: Privileged Architecture, Version 1.12](https://github.com/riscv/riscv-isa-manual/releases/Priv-v1.12)

The go language did not support SV57 mode for RISC-V, this was fixed after release 1.19. This means that any go program compiled with a go version without the fix will crash when run on a system using SV57 mode. (See [Go issue](https://go-review.googlesource.com/c/go/+/409055), [commit introducing the fix](https://github.com/golang/go/commit/1e3c19f3fee12e5e2b7802a54908a4d4d03960da).)  
Linux Kernel v5.18 introduced support for SV57 mode. (See [kernel 5.18 changelog](https://cdn.kernel.org/pub/linux/kernel/v5.x/ChangeLog-5.18), [concerned commit](https://git.kernel.org/pub/scm/linux/kernel/git/riscv/linux.git/commit/?h=for-next&id=aa5b537b0ecc16992577b013f11112d54c7ce869).)  

The Debian image found above, for example, is based on Debisn Sid, or unstable. Meaning, the kernel is fairly up-to-date, above v5.18.
QEMU, when launched with the `virt`board, will be in SV57 mode. I think it should be possible to change the MMU mode by launching a board, or using a CPU that does not use SV57 using the --machine and --cpu flags (such as the Sifive Hi-Five Unleashed board, using a U54 CPU), but even when trying that the emulated MMU used SV57 mode in my case.

You can find in the releases a patched [go 1.19 binary](https://github.com/chefmtt/riscv64/releases/download/v0.1/go1.19beta1-264-g1e3c19f3fe.linux-riscv64.tar.xz) which integrates the fix and a version of [Docker](https://github.com/chefmtt/riscv64/releases/download/v0.1/docker-20.10.17_riscv64.deb) built with this go binary.  
**Warning** : The docker package was linked against GLIBC 2.33 and will need to be patched using patchELF to run on a system with an older version, [instrusctions here]() (swap the link to the docker package).

Some useful commands :

```bash
# Check kernel version
uname -r
# Check MMU mode
cat /proc/cpuinfo
# Or
grep --max-count=1 'mmu' /proc/cpuinfo
# Check QEMU's available CPUs
qemu-system-riscv64 --cpu help
# Check QEMU's available boards
qemu-system-riscv64 --machine help
```
[QEMU RISC-V doc](https://www.qemu.org/docs/master/system/target-riscv.html)

# Build everything from source

## Build a Go release
Refer to the [Build Golang guide](https://github.com/chefmtt/riscv64/blob/main/build-go/build-go.md).

## Build a Docker release
Refer to the [Build Docker guide](https://github.com/chefmtt/riscv64/blob/main/build-docker/build-docker.md).

## Build a K3s release
Refer to the [Build K3s guide](https://github.com/chefmtt/riscv64/blob/main/build-k3s/build-k3s.md).

##  Acknowledgement
This project has received funding from the Key Digital Technologies Joint Undertaking (KDT JU) under grant agreement No 877056. The JU receives support from the European Union’s Horizon 2020 research and innovation programme and Spain, Italy, Austria, Germany, Finland, Switzerland.

![FRACTAL Logo](https://cloud.hipert.unimore.it/apps/files_sharing/publicpreview/jHmgbEb2QJoe8WY?x=1912&y=617&a=true&file=fractal_logo_2.png&scalingup=0)

![EU Logo](https://cloud.hipert.unimore.it/apps/files_sharing/publicpreview/pessWNfeqBfYi3o?x=1912&y=617&a=true&file=eu_logo.png&scalingup=0)
![KDT Logo](https://cloud.hipert.unimore.it/apps/files_sharing/publicpreview/yd7FgKisNgtLPTy?x=1912&y=617&a=true&file=kdt_logo.png&scalingup=0)  
