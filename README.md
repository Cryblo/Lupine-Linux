# Lupine: Linux in Unikernel Clothes

## NOTE
The container image for building the kernel (Linux 4.0) is not available anymore but you can use an older Ubuntu image to build it. We use apline-variant applications that use musl-libc for our benchmarks. PRs are welcomed!

**[Paper](https://dl.acm.org/doi/10.1145/3342195.3387526)**

```
@inproceedings{10.1145/3342195.3387526,
author = {Kuo, Hsuan-Chi and Williams, Dan and Koller, Ricardo and Mohan, Sibin},
  title = {A Linux in Unikernel Clothing},
  year = {2020},
  isbn = {9781450368827},
  publisher = {Association for Computing Machinery},
  address = {New York, NY, USA},
  url = {https://doi.org/10.1145/3342195.3387526},
  doi = {10.1145/3342195.3387526},
  booktitle = {Proceedings of the Fifteenth European Conference on Computer Systems},
  articleno = {Article 11},
  numpages = {15},
  location = {Heraklion, Greece},
  series = {EuroSys ’20}
}
```

In this project, we manually configure the Linux kernel so that it becomes
as small as 4+MB. We aim to show that it has the following unikernel properties
- no mode switching (by KML)
- specialization (by Kconfig)
- size (by evaluation)
- efficiency in terms of boot time and memory density (by evaluation)

## Contribution
- a combination of existing Linux configuration specialization and KML
  for a first-step Linux unikernel
- an evaluation of what unikernel properties are achieved
- a discussion of common unikernel tradeoffs (e.g., no smp, no fork)
  and their impact
- highlighting next steps for Linux specialization/ cross domain
  optimization

## Setup
Clone project:
`git clone https://github.com/hckuo/Lupine-Linux.git`

Update submodule:
`git submodule update --init`

Pull a docker image that contain the environment for building Linux 4.0, and create a tag `linuxbuild:latest` refer to this image:
`docker tag source:tag linuxbuild:latest`, you can download the image that I used: `docker pull a74731248/linuxbuild:latest`

Run `make` command in the content `load_entropy` to generate `load_entropy` file.

Build rootfs:
`sh image2rootfs.sh app tag ext2`, the tag must be `alpine`, because Lupine use musl libc rather than glibc.

Run Lupine-Linux:
- run `sh firecrackerd.sh` in first shell
- run `sh firecracker-run.sh path_to_kernel path_to_rootfs init` in second shell, the `init` can be `/bin/sh` or some scripts that you want to run after Lupine bootup.

## Files
```
scripts
|-- build-kernels.sh (build all kernels for helloworld, redis and ngnix for all variants)
|-- firecrackerd.sh (wrapper firecracker daeomn)
|-- firecracker-run.sh (wrapper of firecrakcer client)
|-- image2rootfs.sh (create userspace root fs from docker image)
|-- firecracker-lz4bench-run.sh (runs lz4 bench as a firecracker microvm lupine+kml+mmio)
`-- run-helper.sh (shared variables and helper functions)
```
> Theses scripts should be executed at the root directory.

## Takeaways:
- don't rewrite Linux unikernel people!
- know what unikernel benefits you really care about and what you give
  up to get them
- future work should be on app manifests LTO, etc.

## Going forward beyond unikernel restrictions
- smp vs. non-smp
  - smp gives speed benefits, esp when CPU bound
  - may cause size overhead (different config)?
- fork vs. non-fork
  - same as smp just may add context switches (show in microbenchmark)
- (how many applications use fork?)
- dynamic linking vs. static
- with KML unikernel properties "gracefully degrade"


