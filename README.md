# Self-use kernel

Based on the Debian Cloud Kernel configuration file

## Installation

```shell
# You need to install curl, wget and jq first.
wget $(curl -s https://api.github.com/repos/love4taylor/bbr3-kernel-pkg/releases/latest  | jq -r '.assets[] | select(.name | contains ("deb")) | .browser_download_url')
dpkg -i linux-headers-*.deb
dpkg -i linux-image-*.deb
dpkg -i linux-libc-dev_*.dev #optional
# Then reboot
```

## Patchs

- [Netfilter nf_tables FullCone](https://gitlab.com/xanmod/linux-patches/-/blob/master/linux-6.7.y-xanmod/net/netfilter/0001-netfilter-Add-netfilter-nf_tables-fullcone-support.patch?ref_type=heads)
- [Netfilter xt_FLOWOFFLOAD](https://gitlab.com/xanmod/linux-patches/-/blob/master/linux-6.7.y-xanmod/net/netfilter/0002-netfilter-add-xt_FLOWOFFLOAD-target.patch?ref_type=heads)
- [BBRv3](https://gitlab.com/xanmod/linux-patches/-/tree/master/linux-6.7.y-xanmod/net/tcp/bbr3?ref_type=heads)
- [Cloudflare: Add a sysctl to skip tcp collapse processing when the receive  buffer is full](https://gitlab.com/xanmod/linux-patches/-/blob/master/linux-6.7.y-xanmod/net/tcp/cloudflare/0001-tcp-Add-a-sysctl-to-skip-tcp-collapse-processing-whe.patch?ref_type=heads) ([How-to-use](https://blog.cloudflare.com/optimizing-tcp-for-high-throughput-and-low-latency/))
- [Clear Linux Patchs](https://github.com/clearlinux-pkgs/linux) (Exclude 0132, 0118, 0113, 0138, 0139)
- [TCP Brutal](https://gist.github.com/love4taylor/111d56cd2b1dc149cba6d80f617f47b1)

## Notice

1. It is recommended to add `quiet console=tty0 console=ttyS0,115200n8 cryptomgr.notests initcall_debug intel_iommu=igfx_off kvm-intel.nested=1 no_timer_check noreplace-smp page_alloc.shuffle=1 rcupdate.rcu_expedited=1 rootfstype=ext4,btrfs,xfs,f2fs tsc=reliable rw` to the boot cmdline if you are using an **Intel** CPU.
2. **The kernel has a built-in TCP Brutal module, please do not use the official script to install the DKMS module at the same time.**
