#  EGOIST Kernel for Debian/Ubuntu

Based on the Debian generic and cloud kernel configuration file

## Support Microarchitecture

Since version `6.7.7`, the kernel will minimally support the x86-64-v2 Microarchitecture.

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
wget -q --show-progress $(wget -q -O - https://api.github.com/repos/love4taylor/linux-egoist-deb/releases/latest | jq -r '.assets[] | select(.name | contains ("deb")) | select(.name | contains ("cloud")) | .browser_download_url')
sudo dpkg -i linux-headers-*-egoist-cloud_*.deb
sudo dpkg -i linux-image-*-egoist-cloud_*.deb
```

else

```
wget -q --show-progress $(wget -q -O - https://api.github.com/repos/love4taylor/linux-egoist-deb/releases/latest | jq -r '.assets[] | select(.name | contains ("deb")) | select(.name | contains ("generic")) | .browser_download_url')
sudo dpkg -i linux-headers-*-egoist-generic_*.deb
sudo dpkg -i linux-image-*-egoist-generic_*.deb
```

fi

## Patches

- Graysky's Kernel patch enables compiler optimizations for additional CPUs
- Broadcom fullcone NAT from ASUS Merlin (IPv4 only)
- Netfilter FLOWOFFLOAD target
- BBRv3
- Cloudflare: Add a sysctl to skip tcp collapse processing when the receive buffer is full ([How-to-use](https://blog.cloudflare.com/optimizing-tcp-for-high-throughput-and-low-latency/))
- TCP Brutal
- Partial Clear Linux patches

## Notice

1. **This kernel has a built-in TCP Brutal module, please do not use the official installation script to install the DKMS module.**
2. I **hardcoded** the enable parameter for fullcone to prevent recompiling iptables or adding nftables parameters to use fullcone, so you can use MASQUERADE as usual, and it will **force** to using fullcone.

