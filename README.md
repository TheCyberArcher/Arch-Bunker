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

> I much prefer firewalld and we will base it on that case :)

```yay -S firewalld``` (install the firewalld package) \
```systemctl enable firewalld```(enable the firewalld service at startup) \
```systemctl start firewalld``` (start the firewalld daemon) \
```firewall-cmd --set-default-zone=block``` (set the default zone to block)

> With these elements, you already have a fairly significant limitation of network flows. The firewalld preconfiguration is very correct, you can improve it by adding rules if necessary.

## Step 4 : Use custom network parameters with a VPN



## Step 5: Enable Apparmor to protect your system

> [AppArmor](https://www.apparmor.net/) use a "Mandatory Access Control" to prevent external or internal threats and zero-day attacks. [Ubuntu](https://ubuntu.com/server/docs/apparmor) has made the choice to implement this security measure for a long time and [Debian](https://wiki.debian.org/AppArmor/HowToUse) has also followed suit. I think it is a good thing to add this layer of security. Thanks to AppArmor, a binary/process will only be limited to what it really needs and will normally not access critical/unnecessary resources.

> Easier to administer than SE Linux, there remains the problem that AppArmor needs a specific profile per binary. A community project ([apparmor.d](https://github.com/roddhjav/apparmor.d)) has been created to add all the common profiles and reinforce them with a few commands.

```yay -S apparmor``` (install the base package of apparmor)
```cd /boot/loader/entries/``` (go to the concerned directory)

Edit the file corresponding to your kernel loader conf (main, dont modify the fallback configuration file)

Add this option parameter ```lsm=landlock,lockdown,yama,integrity,apparmor,bpf```

<br />

Enable the apparmor service ```sudo systemctl enable apparmor.service```

Reboot your computer and try if apparmor is running : 

```sudo aa-status``` 

Install the package [apparmor.d-git](https://aur.archlinux.org/packages/apparmor.d-git) for the desktop hardening :

```yay -S apparmor.d-git```

Note : Important: I advise you to install it without activating the hardening, first test a hardening of the applications step by step, if everything works well, we will apply the hardening.

If you dont have bugs, enforce the configuration files : 

```sudo aa-enforce /etc/apparmor.d/*```

Check the number of enforced configuration files with : 

```sudo aa-status | grep -i enforce```


## Step 6: Application Sandboxing with Firejail

> [Firejail](https://github.com/netblue30/firejail) is a program that reduces the risk of security breaches by restricting the running environment of untrusted applications using Linux namespaces and seccomp-bpf. It allows a process and all its descendants to have their own private view of the globally shared kernel resources, such as the network stack, process table, mount table.

> [Firejail](https://github.com/netblue30/firejail) allows you to sandbox all applications on your system without significantly affecting performance and making it transparent to the end user (Very useful if you download AUR packages or from unverified sources, or proprietary softwares). Often criticized since it uses SUID, I would like to remind you that in desktop use, we want to avoid zero-day vulnerabilities, malware, data uploads and access to your personal files. In no case should a possible privilege escalation really worry you, you gain more by confining your applications than by leaving them as such. If this type of threat is your concern or you are wanted by intelligence, opt for [Qubes OS](https://www.qubes-os.org/) or something else.

Install Firejail on your computer : ```yay -S firejail```

Add firejail by default for all supported apps : ```sudo firecfg```

Add the AppArmor support to enforce the security : 

```sudo apparmor_parser -r /etc/apparmor.d/firejail-default```\
```sudo aa-enforce firejail-default```

Edit this configuration file and uncomment the "apparmor" line: ```/etc/firejail/firejail.config```

Check the application sandboxing : ```firejail --list```

## Step 7 : Enhance Flatpak application security

> Flatpak applications are the revival of distribution in the Linux world. Here you get your programs from Flathub most of the time and simply install. Although the most famous Flatpaks are well secured, the system of non-unified dependencies and the fact that no maintainer of your distribution has checked the package, can expose you to significant security risks. Add to that the fact that many private companies publish flatpaks directly, you may have serious doubts about the confidentiality of your data.

> I invite you to read a very good article on the subject: [Flatpak - a security nightmare](https://flatkill.org/)

To limit these problems as much as possible, I recommend two things:

- Schedule a cron task with : ```flatpak update```
- Install [flatseal](https://github.com/tchx84/Flatseal) to limit apps privileges : ```flatpak install flathub com.github.tchx84.Flatseal```

For each application, limit its rights with flatseal (access to folders, microphone, camera, services) and try to use software approved by the community. With this manipulation and automatic updates, you will limit the risks as much as possible. Otherwise, simply do not use Flatpak.

##  Step 8 : Only use safe and privacy friendly softwares
