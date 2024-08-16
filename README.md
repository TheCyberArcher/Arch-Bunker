# Arch-Hardening

<br />

> Hello everyone, I offer you here a guide for the Archlinux distribution, to make it more robust and resist malicious code from untrusted applications. This guide will also improve your privacy and will not hinder your daily use of your machine. I know how difficult it is to find information about Linux security on the web, there is basic advice, but I would like to be able to bring you more and what is there to allow you to improve your security, without going through extremes like QubesOS or Tails, which frankly are not ok for everyday work.


<br />

## Step 1: Apply the basic recommendations

> As I explained to you, after an installation of archlinux, even with the desktop you want, you have almost nothing as a security measure. The distribution, like others, tries to be close to the KISS philosophy and the original concepts of UNIX. So you will not have any preconfiguration, but the guide from arch will guide you to do the steps that I will list below.

- Choose a secure password
- Apply the latest security updates and shecule a [vulnerability check](https://archlinux.org/packages/extra/x86_64/arch-audit/)
- Encrypt your drive with [LUKS](https://wiki.archlinux.org/title/Dm-crypt/Encrypting_an_entire_system) (If you don't want to encrypt everything, at least encrypt your data disks)
- Disable [automount](https://wiki.archlinux.org/title/Fstab) for data luks drives
- Add [sudo](https://wiki.archlinux.org/title/Sudo) for your user account and limit the root usage

<br />

I'm only listing things that seem essential here, but you can find information via the [archwiki](https://wiki.archlinux.org/title/Security).

## Step 2: Would you like the Hardened Kernel ?

> As explained in the documentation, the [linux-hardened kernel](https://archlinux.org/packages/extra/x86_64/linux-hardened/) comes with hardening patch set and more security-focused compile-time configuration options than the linux package (mitigate kernel and userspace exploits). A custom build can be made to choose a different compromise between security and performance than the security-leaning defaults.

> Most of the stuff is critical security additions that weren't accepted into the base kernel by default. This is a good thing, but it can be buggy, but if you want the most secure option possible (which I probably doubt on a daily basis, since your threat model won't be the same for a server) you can install it. 

Personnaly, i use just the [Zen-Kernel](https://archlinux.org/packages/extra/x86_64/linux-zen/) for my daily use.

## Step 3: Chose and configure a robust Firewall

> Implementing a firewall is a basic security measure. Arch comes without any strict measures and seriously, I advise you to install one. You have tons of possibilities, with that you will be able to limit connections and network calls from potentially unwanted / malicious applications. Believe me, those of your internet box are not enough, here we will limit the attack surface by reducing the possibilities of an attack / evasion by the network.

- [Ip tables](https://wiki.archlinux.org/title/Iptables) (Use Netfilter, iptables is also commonly used to refer to this kernel-level firewall)
- [Firewalld](https://wiki.archlinux.org/title/Firewalld) (Firewalld is a firewall daemon developed by Red Hat. It uses nftables by default)

I much prefer firewalld and we will base it on that case :)

```yay -S firewalld```\
```systemctl enable firewalld```\
```systemctl start firewalld```\
```firewall-cmd --set-default-zone=block```\
