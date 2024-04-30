# Self-use kernel

Based on the Debian generic and cloud kernel configuration file

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

You need to have `wget` and `jq` installed in advance.

If [ you are using a server and don't need the drivers required by bare metal machines ]; then

```
wget -q --show-progress $(wget -q -O - https://api.github.com/repos/love4taylor/linux-self-use-deb/releases/latest | jq -r '.assets[] | select(.name | contains ("deb")) | select(.name | contains ("cloud")) | .browser_download_url')
sudo dpkg -i linux-headers-*-egoist-cloud_*.deb
sudo dpkg -i linux-image-*-egoist-cloud_*.deb
```

else

```
wget -q --show-progress $(wget -q -O - https://api.github.com/repos/love4taylor/linux-self-use-deb/releases/latest | jq -r '.assets[] | select(.name | contains ("deb")) | select(.name | contains ("generic")) | .browser_download_url')
sudo dpkg -i linux-headers-*-egoist-generic_*.deb
sudo dpkg -i linux-image-*-egoist-generic_*.deb
```

fi

### pre-release

Pre-release kernels typically require testing and are not recommended for regular users to install.

```
EGOIST_PREVER=$(wget -q -O - https://api.github.com/repos/love4taylor/linux-self-use-deb/releases | jq -r 'map(select(.prerelease)) | first | .tag_name')
echo $EGOIST_PREVER
# If the value is null, it means there is no pre-release. Please do not continue to execute the installation command below.

# Cloud kernel
wget -q --show-progress $(wget -q -O - https://api.github.com/repos/love4taylor/linux-self-use-deb/releases/tags/$EGOIST_PREVER | jq -r '.assets[] | select(.name | contains ("deb")) | select(.name | contains ("cloud")) | .browser_download_url')
sudo dpkg -i linux-headers-*-egoist-cloud_*.deb
sudo dpkg -i linux-image-*-egoist-cloud_*.deb

# Generic kernel
wget -q --show-progress $(wget -q -O - https://api.github.com/repos/love4taylor/linux-self-use-deb/releases/tags/$EGOIST_PREVER | jq -r '.assets[] | select(.name | contains ("deb")) | select(.name | contains ("generic")) | .browser_download_url')
sudo dpkg -i linux-headers-*-egoist-generic_*.deb
sudo dpkg -i linux-image-*-egoist-generic_*.deb
```

## Patchs

- [kernel_compiler_patch](https://github.com/graysky2/kernel_compiler_patch)
- Broadcom fullcone NAT from [ASUS Merlin](https://github.com/RMerl/asuswrt-merlin.ng)
- [BBRv3](https://gitlab.com/xanmod/linux-patches/-/tree/master/linux-6.8.y-xanmod/net/tcp/bbr3?ref_type=heads)
- [Cloudflare: Add a sysctl to skip tcp collapse processing when the receive  buffer is full](https://gitlab.com/xanmod/linux-patches/-/blob/master/linux-6.8.y-xanmod/net/tcp/cloudflare/0001-tcp-Add-a-sysctl-to-skip-tcp-collapse-processing-whe.patch?ref_type=heads) ([How-to-use](https://blog.cloudflare.com/optimizing-tcp-for-high-throughput-and-low-latency/))
- [TCP Brutal](https://github.com/love4taylor/linux-self-use-deb/blob/master/patches/others/0001-net-tcp_brutal-make-it-as-a-built-in-kernel-module.patch)
- [Latest ZSTD](https://github.com/CachyOS/kernel-patches/blob/master/6.8/0008-zstd.patch)
- [Userspace KSM](https://github.com/CachyOS/kernel-patches/blob/master/6.8/0007-ksm.patch) (You need build [uksmd](https://codeberg.org/pf-kernel/uksmd) by yourself)
- [Clearlinux](https://github.com/clearlinux-pkgs/linux)

## Notice

1. **The kernel has a built-in TCP Brutal module, please do not use the official script to install the DKMS module at the same time.**
2. To avoid having to recompile iptables, I've [**hardcoded**](https://github.com/love4taylor/linux-self-use-deb/blob/1584f29602cb48ba1045ab0084fe205baf20ce2b/patches/others/0001-netfilter-nat-add-brcm-fullcone-support-from-ASUS.patch#L245-L250) fullcone to be enabled, so you can just use MASQUERADE as usual and it will **force** to fullcone.
