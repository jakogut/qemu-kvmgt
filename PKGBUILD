# Maintainer: ajs124 < aur at ajs124 dot de>
# Contributor: Devin Cofer <ranguvar{AT]archlinux[DOT}us>
# Contributor: Tobias Powalowski <tpowa@archlinux.org>
# Contributor: Frederic Bezies <fredbezies@gmail.com>
# Contributor: Sébastien "Seblu" Luttringer <seblu@seblu.net>

pkgbase=qemu-git
_gitname=qemu
pkgname='qemu-kvmgt'
pkgver=2015Q3
pkgrel=1
arch=('i686' 'x86_64')
license=('GPL2' 'LGPL2.1')
url='http://wiki.qemu.org/'
makedepends=('pixman' 'libjpeg' 'libpng' 'sdl' 'alsa-lib' 'nss' 'glib2'
             'gnutls' 'bluez-libs' 'vde2' 'util-linux' 'curl' 'libsasl'
             'libgl' 'libpulse' 'libcap-ng' 'libaio' 'libseccomp'
             'libiscsi' 'python2' 'iasl'
             'usbredir' 'ceph' 'glusterfs' 'libssh2' 'lzo' 'snappy'
             'dtc' 'libepoxy' 'git' 'texi2html' 'perl' 'numactl')
source=(qemu::git+https://github.com/01org/igvtg-qemu#branch=kvmgt_public2015q3
        qemu.sysusers
        qemu-ga.service
        65-kvm.rules
        qemu.install
	seabios-gcc5.patch)
sha256sums=('SKIP'
            'dd43e2ef062b071a0b9d0d5ea54737f41600ca8a84a8aefbebb1ff09f978acfb'
            '0b4f3283973bb3bc876735f051d8eaab68f0065502a3a5012141fad193538ea1'
            '60dcde5002c7c0b983952746e6fb2cf06d6c5b425d64f340f819356e561e7fc7'
            '0df69a77645c9a05f98635773666b6212084525d7801ef8382242b06baebe5aa'
            '091f5eed7e33b45cabb232e9a03dd6c1abae1a820b804c888c20d4b7e673618f')
build() {
  cd $_gitname

  git submodule update --init dtc
  git submodule update --init roms/seabios

  # qemu vs. make 4 == bad
  export ARFLAGS="rv"
  # http://permalink.gmane.org/gmane.comp.emulators.qemu/238740
  export CFLAGS+=' -fPIC'
  export CFLAGS+=' -Wno-error'
  # gtk gui breaks keymappings at the moment
  ./configure --prefix=/usr --prefix=/usr --sysconfdir=/etc --audio-drv-list='pa alsa sdl' \
	--python=/usr/bin/python2 --smbd=/usr/bin/smbd \
	--enable-kvm --disable-xen --enable-sdl --target-list=x86_64-softmmu

  make V=99

  cd roms/seabios
  patch -p2 -i ${srcdir}/seabios-gcc5.patch
  LC_ALL=C make
}

package() {
  pkgdesc='A generic and open source processor emulator which achieves a good emulation speed by using dynamic translation. Git version.'
  depends=('pixman' 'libjpeg' 'libpng' 'sdl' 'libgl'
           'gnutls' 'bluez-libs'
           'usbredir' 'lzo' 'snappy' 'libpulse' 
           'dtc' 'numactl' 'libnfs' 'libepoxy')
  optdepends=('ovmf: Tianocore UEFI firmware for qemu'
              'samba: SMB/CIFS server support')
  conflicts=('qemu' 'kvm' 'kvm-git' 'qemu-spice' 'seabios' 'seabios-git')
  provides=('qemu' 'qemu-kvm' 'qemu-spice' 'seabios' 'seabios-git')

  options=(!strip)
  install=qemu.install

  make -C $_gitname DESTDIR="${pkgdir}" libexecdir="/usr/lib/qemu" install

  cd "${pkgdir}"
  install -D -m755 ${srcdir}/$_gitname/roms/seabios/out/bios.bin usr/share/qemu/bios.bin

  # systemd stuff
  install -D -m644 "${srcdir}/65-kvm.rules" usr/lib/udev/rules.d/65-kvm.rules
  install -D -m644 "${srcdir}/qemu.sysusers" usr/lib/sysusers.d/qemu.conf
}

# vim:set ts=2 sw=2 et:
