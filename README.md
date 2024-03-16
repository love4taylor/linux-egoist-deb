# Self-use kernel

Based on the Debian generic kernel configuration file

## Support Microarchitecture

Starting with 6.7.7, the kernel will minimally support the x86-64-v2 Microarchitecture.

```
love4taylor@sony-nuro-n1:~$ /lib64/ld-linux-x86-64.so.2 --help | grep supported
  x86-64-v3 (supported, searched)
  x86-64-v2 (supported, searched)
  haswell (AT_PLATFORM; supported, searched)
  tls (supported, searched)
  x86_64 (supported, searched)
```

## Installation

```shell
# You need to install curl, wget and jq first.
wget $(curl -s https://api.github.com/repos/love4taylor/linux-self-use-deb/releases/latest  | jq -r '.assets[] | select(.name | contains ("deb")) | .browser_download_url')
dpkg -i linux-headers-*.deb
dpkg -i linux-image-*.deb
dpkg -i linux-libc-dev_*.dev #optional
# Then reboot
```

## Patchs

- [kernel_compiler_patch](https://github.com/graysky2/kernel_compiler_patch)
- Broadcom fullcone NAT from [ASUS Merlin](https://github.com/RMerl/asuswrt-merlin.ng)
- [Netfilter xt_FLOWOFFLOAD](https://gitlab.com/xanmod/linux-patches/-/blob/master/linux-6.8.y-xanmod/net/netfilter/0002-netfilter-add-xt_FLOWOFFLOAD-target.patch?ref_type=heads)
- [BBRv3](https://gitlab.com/xanmod/linux-patches/-/tree/master/linux-6.8.y-xanmod/net/tcp/bbr3?ref_type=heads)
- [Cloudflare: Add a sysctl to skip tcp collapse processing when the receive  buffer is full](https://gitlab.com/xanmod/linux-patches/-/blob/master/linux-6.8.y-xanmod/net/tcp/cloudflare/0001-tcp-Add-a-sysctl-to-skip-tcp-collapse-processing-whe.patch?ref_type=heads) ([How-to-use](https://blog.cloudflare.com/optimizing-tcp-for-high-throughput-and-low-latency/))
- [Clear Linux Patchs](https://github.com/clearlinux-pkgs/linux) (Exclude 0132, 0118, 0113, 0138, 0139)
- [TCP Brutal](https://gist.github.com/love4taylor/111d56cd2b1dc149cba6d80f617f47b1)

## Notice

1. It is recommended to add `quiet console=tty0 console=ttyS0,115200n8 cryptomgr.notests initcall_debug intel_iommu=igfx_off kvm-intel.nested=1 no_timer_check noreplace-smp page_alloc.shuffle=1 rcupdate.rcu_expedited=1 rootfstype=ext4,btrfs,xfs,f2fs tsc=reliable rw` to the boot cmdline if you are using an **Intel** CPU.
2. **The kernel has a built-in TCP Brutal module, please do not use the official script to install the DKMS module at the same time.**
3. To avoid having to recompile iptables, I've [**hardcoded**](https://github.com/love4taylor/linux-self-use-deb/blob/ad1fcc5bb6c0da7f7584c029584f7a9f82f30ed4/0001-netfilter-nat-add-brcm-fullcone-support-from-ASUS.patch#L245-L249) fullcone to be enabled, so you can just use MASQUERADE as usual and it will **force** to fullcone.
