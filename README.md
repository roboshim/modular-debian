# modular-debian
MoDe - modular debian (originaly mini-debian)

## Idea

I love Linux live distros, mainly Knoppix. But it's not so fun, if such a
distro runs on broken CD/DVD drive or on old DVD/CD-RW medium or on not so
fast USB flash.

I know there is a SLAX, brilliant Idea for modular Linux. It is live OS too,
and it is minimalistic (runs on 48MB RAM or 256MB for KDE).

### But:

  * SLAX is based on Slackware, I use Debian.
  * For installed (non-live) system Debian Linux requires about 200MB too.
    That's OK. However every server or PC has today at least 2GB RAM, even PC
    Engines APU2 has 2GB or 4GB and can run even ESXi. So I have plenty of RAM
    available
  * I want to start Linux without need of some special medium (CD, DVD, USB).
    The best way is to start from Network. (NFS is one solution, but it needs
    NFS server, permanent access to this NFS server, not the same as live-OS)
  * I wanted to create something very fast and easy to prepare.

### So:

I have seen the code in the linuxrc script in initramfs of Debian. They make

	mount -t $ROOTFSTYPE $ROOTFLAGS $ROOT /target

What if there is a ext3/4 file image with some base debian system (created
using debootstrap + few important packages) included in the initramfs? Then I
could set **ROOTFSTYPE=ext3**, **ROOTFLAGS=loop** and **ROOT=/debian.img** and
it should mount this file using loop device. Steps to prepare:

  * Create file image with debian basic system using debootstrap - easy+fast
  * Install a few important packages in this file image using mount -o loop,
    chroot and apt-get - easy+fast
  * Copy this image file to initramfs - easy+fast
  * Run kernel with modified initramfs from Grub, Syslinux (ISOlinux,
    PXEboot i.e. from network) - easy+fast
  * This will load and store the big image in RAM, but 200MB of 2GB is
    nothing.

OK, it was done really easy and fast. But of course it was not perfect:

  * The initramfs was big, containing whole Debian system (of course gziped
    cpio, but still a few MB) - pxeboot needs a few minutes to download
    initramfs from tftp server.
  * After start, root FS is RO, many services expect some writable non-tmpfs
    persistent folders.
  * Unable to do temporary local changes in config files, unable to install
    new packages.

Then came the idea. System based on Debian, on standard tools included in
initramfs with as few as possible extending scripts to allow get file image
(either during tftp download together with Linux kernel and initramfs or using
busybox wget to get the image from webserver running on tftp server) created
as squashfs with Debian system and mount this image into AUFS/UnionFS as
rootfs (this looks like what SLAX do). Then I have written first script for
preparing and update Debian image files for Squeeze and Wheezy, and some
scripts to mount this squashfs image to aufs. I have versions for i386 and
amd64, console-only and with LXDE. It means total there are 8 different images
with mostly the same packages (console version and X version contain the same
command line tools).

Later I needed to have some basic system and create some extending images to
give more features and fixed configuration. I have updated the scripts to
allow downloading and merge-mounting multiple images. Now it looks really like
SLAX.

So I wanted to make new script which would create modular system.

## Design ##

As **base** image will be used debootstrap image. SquashFS will be created. This
is the only image which will be created without aufs and underlaying squashfs
images.

The second image **essential** will contain some important basic packages,
e.g. kernel, aufs-tools, squashfs-tools, to allow run the **base** image.
Creating this image means to mount **base** squashfs image to aufs mount,
install this extending packages and create second squashfs only from the
changed branch (RW branch) of aufs.

The next images will be based on some existing modules, e.g. for **console**
image the **base** squashfs image and **essential** squashfs image will be
mounted to aufs, together with empty RW branch. In this mounted aufs will be
installed console tools and created configuration. From this RW branch will be
created squashfs image **console**.

On the **console** image (and the lower images) can be based the **lightx**
image, with only light WM, e.g. LXDE, and web browser. On **lightx** image can
be based **fullx** image with libreoffice, gimp etc.

The images will be big, but then you have surely at least 8GB RAM for
**fullx** image.

The images will be downloaded to RAM, mounted each as simple squashfs and
then all together mounted to aufs. The RW branch of aufs will be mounted to
tmpfs somewhere to rootfs so the user can generate own squashfs from currently
running system and then download next time.

Now how to specify the order of images. My idea is to assign shot name for
every level and the image name with names from 1st level to nth level, e.g.:

  * first level image is **base**
  * second level image is **essential**, i.e. whole image name is
    **base-essential.sqfs.img**
  * third level image is **console**, i.e. whole image name is
    **base-essential-console.sqfs.img**
  * fourth level image is **lightx**, i.e. whole image name is
    **base-lightx-console-essential.sqfs.img**
  * there can be other fourth level image named **smtp** working as SMTP
    server with fixed config, based on **console** image, i.e. whole image name
    is **base-essential-console-smtp.sqfs.img**

And so on. There is no limit on level depth and which images are the images
based on.

If the underlaying image will be updated, all images based on this image
should be recreated too, but not required, in case that some image contains
only configuration files. To recreated the image means to mount underlaying
images to aufs and do changes - install new packages, create/modify some files.

## This "project" ##

I want to create hopefully a few simple scripts to be able easy and "fast" to
create these images.

One script should create and update the images.

Other scripts (and maybe configuration files) should do downloading and
mounting the images. Some of them I can take from the old version.

Maybe I will add the possibility to mount NFS folders or local drives, which
could simplify storing own configuration or use same home folder. Or maybe to
connect home/working folder over sshfs. But these are only ideas and uncertain
future.

## Important note ##

I have created this project mainly for me. I want to create this script. But I
have not enough time for this "project". But this "project" is and will be
released under GNU GPL so you can take, modify and distribute it. If you will
find this "project" interesting and you will want to participate some day, of
course you can send me some updates, but I am not sure, if I will have enough
time to merge them. So maybe you will prefer clone, develop and release your
own version.
