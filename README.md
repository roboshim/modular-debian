# modular-debian
MoDe - modular debian (originaly mini-debian)

## Idea

I love Linux live distros, mainly Knoppix. But it's not so fun, if such a distro runs on broken CD/DVD drive or on old DVD/CD-RW or on not so fast USB.

I know there is a SLAX, brilliant Idea for modular Linux. It is live OS too, and it is minimalistic (runs on 48MB RAM or 256MB for KDE). "But" it is a slackware.

But:

	* I use Debian
	* For installed (non-live) system Debian Linux requires about 200MB too. That's OK. However every server or PC has today at least 2GB RAM, even PC Engines APU2 has 2GB or 4GB and can run even ESXi. So I have plenty of RAM available
	* I need start Linux without need of some special medium (CD, DVD, USB). The best way is to start from Network. (NFS is one solution, but needs NFS server, access the NFS server, not the same as live-OS)
	* I wanted to create something very fast, easy to prepare.

So:

	* I have seen the code in the linuxrc script in initramfs of Debian. They make

			mount -t FS DEVICE /target
