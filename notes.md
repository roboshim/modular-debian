# Notes #

this file contains notes for development


## Directory structure ##

    modular-debian:
    +-- resources:
    |   | // resources for modular-debian
    |   +-- scripts:
    |   |   | // scripts to run out of chroot to allow run on preparing system
    |   |   | // or to make chroot and run in the chrooted system, maybe some
    |   |   | // "pre" and "post" script sctructure will be required to run
    |   |   | // e.g. "pre-install-packages" and "post-install-packages"
    |   |   +-- ALL:
    |   |   |   | // this scripts will be run for every image, the parameters
    |   |   |   | // will tell the image name
    |   |   |   +-- 01-install-base-files-pre
    |   |   |   |   // this *default* script installs files from resources/files
    |   |   |   |   // to destination system before package installation
    |   |   |   +-- 02-run-in-chroot
    |   |   |   |   // this script can chroot to destination system and run there
    |   |   |   |   // some script
    |   |   |   +-- 03-install-packages
    |   |   |   |   // this *default* script chroots to destination system and
    |   |   |   |   // installs packages listed in resources/packages/*.txt
    |   |   |   +-- 04-install-base-files-post
    |   |   |   |   // this *default* script installs files from resources/files
    |   |   |   |   // to destination system after package installation
    |   |   +-- base:
    |   |   |   | // scripts for *base* image
    |   |   +-- base-essential:
    |   |   |   | // scripts for *base-essential* image
    |   |   +-- base-essential-console:
    |   |   |   | // scripts for *base-essential-console* image
    |   +-- files:
    |   |   | // files to be copies into the image, maybe some "pre" and "post"
    |   |   | // structure will be required, e.g. "pre-install-packages" and
    |   |   | // "post-install-packages"
    |   |   +-- installation:
    |   |   |   | // files required during package installation, e.g.
    |   |   |   | // /usr/sbin/policy-rc.d to disable rc.d scripts invoking
    |   |   |   +-- ALL:
    |   |   |   |   +-- usr:
    |   |   |   |   |   +-- sbin:
    |   |   |   |   |   |   +-- policy-rc.d
    |   |   |   +-- base:
    |   |   |   +-- base-essential:
    |   |   |   +-- base-essential-console:
    |   |   +-- final:
    |   |   |   +-- base:
    |   |   |   |   | // these files should be installed after package
    |   |   |   |   | // installation (package configuration files) for runtime
    |   |   |   +-- base-essential:
    |   |   |   +-- base-essential-console:
    |   |   +-- packages:
    |   |   |   | // files with packages which should be installed in the image
    |   |   |   +-- base
    |   |   |   |   // this *default* file should be empty, base image should
    |   |   |   |   // be base debootstrap image only, but the final decision
    |   |   |   |   // is yours
    |   |   |   +-- base-essential
    |   |   |   |   // *default* file probably only kernel, squashfs-tools,
    |   |   |   |   // aufs-tools, busybox
    |   |   |   +-- base-essential-console
    +-- work:
    |   | // directory with working files
    |   +-- work.PID:
    |   |   | // working folder for running script with PID
    |   |   +-- mnt:
    |   |   |   | // folder for mounts
    |   |   |   +-- sqfs:
    |   |   |   | // folder for squashfs image mounts
    |   |   |   +-- aufs:
    |   |   |   | // folder for aufs mounts
    |   |   +-- aufs-rw:
    |   |   |   | // read-write branch for aufs
    |   |   +-- sparse-images:
    |   |   |   | // sparse image for aufs-rw


## Functionality ##

  * **Image name format**:
    * **_DEBIANNAME_-_ExtName1_-_ExtName2_-_ExtName3_._ARCHITECTURE_**
    * e.g. **wheezy-essential-console-lightx.amd64**
  * **Actions**
    * **make**<br/>
      create or update single image and base images if they do not exist or are
      older than their base image
    * **makeall**<br/>
      create and/or update all base images from specified until **base** without
      some other images based on some of base images of the specified image
    * **updateall**<br/>
      find all existing images and recreate all them from base image *(note: get
      current timestamp on start and recreate all images older than this
      timestamp)*
    * **makefound**<br/>
      create or update all found images, for which the *package* file does exist
      in resources/files/packages
  * **Procedure**
    * base
      * single name stands for debian distro name (**wheezy**, **jessie**,
        **sid**, **stable**, ...)
      * debootstrap to create base system *(note: into current FS or create
        big sparse file, format ext4 and mount and install to this sparse
        image file?)*
    * **base-essential**, **base-essential-console**,
      **base-essential-console-lightx**,
      **_base{N}img_-_base{N-1}img_-_base{N-2}img_-...-_base2img_-_base1img_-_mainimg_**
      * if *base1img* has its base (*base2img*) and *base1img* is older than
        *base2img*, then recreate *base1img*
      * recurrent *mountbaseimg base{I}img* - if the base image of this
        image is not mounted, run *mountbaseimg base{I+1}img*, and then mount
        them to aufs


## Script ##

  * Debian package
  * Check dependencies (for non-Debian installation, e.g. prepare on Gentoo
    with debootstrap)


   
---

vim:set com=fb\:-,fb\:+,fb\:=,fb\:*,fb\:>,fb\:#,fb\:),bn\://,n\:|\ \ \ ,n\:|\ fo=tcqro tw=78 et ts=2 sw=2:
