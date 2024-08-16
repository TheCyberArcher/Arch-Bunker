# Arch-Hardening

<br />

> Hello everyone, I offer you here a guide for the Archlinux distribution, to make it more robust and resist malicious code from untrusted applications. This guide will also improve your privacy and will not hinder your daily use of your machine. I know how difficult it is to find information about Linux security on the web, there is basic advice, but I would like to be able to bring you more and what is there to allow you to improve your security, without going through extremes like QubesOS or Tails, which frankly are not ok for everyday work.


<br />

## Step 1: Apply the basic recommendations

> As I explained to you, after an installation of archlinux, even with the desktop you want, you have almost nothing as a security measure. The distribution, like others, tries to be close to the KISS philosophy and the original concepts of UNIX. So you will not have any preconfiguration, but the guide from arch will guide you to do the steps that I will list below.

- Choose a secure password
- Apply the latest security updates and shecule a vulnerability check
- Encrypt your drive with luks (If you don't want to encrypt everything, at least encrypt your data disks)
- Disable automount for data lunks drives
- Add sudo for your user account and limit the root usage

I'm only listing things that seem essential here, but you can find information via the [archwiki](https://wiki.archlinux.org/title/Security).
