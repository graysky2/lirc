# Maintainer: Lukas Fleischer <lfleischer@archlinux.org>
# Contributor: Paul Mattal <paul@archlinux.org>
# Contributor: Eli Schwartz <eschwartz@archlinux.org>
# Contributor: graysky <graysky AT archlinux DOT us>
#
pkgname=lirc
_pkgver=0.10.1
[[ $_pkgver =~ [a-z]$ ]] && pkgver="${_pkgver:0:-1}.${_pkgver: -1}" || pkgver="$_pkgver"
pkgrel=11
epoch=1
pkgdesc="Linux Infrared Remote Control utilities"
arch=('x86_64')
url="https://www.lirc.org/"
license=('GPL')
depends=('alsa-lib' 'libx11' 'libftdi' 'libusb-compat')
makedepends=('help2man' 'alsa-lib' 'libx11' 'libxslt' 'python' 'python-setuptools'
             'python-yaml' 'systemd')
optdepends=('python: for lirc-setup, irdb-get and pronto2lirc'
            'python-gobject: for lirc-setup and irdb-get'
            'python-yaml: for lirc-setup and irdb-get')
provides=('lirc-utils')
conflicts=('lirc-utils')
replaces=('lirc-utils')
backup=('etc/lirc/lirc_options.conf' 'etc/lirc/lircd.conf' 'etc/lirc/lircmd.conf')
source=("https://prdownloads.sourceforge.net/${pkgname}/${pkgname}-${_pkgver}.tar.bz2"
        lirc-0.10-build-fix.patch
        0001-Replace-the-obsolete-get_event_loop-with-get_running.patch
        0002-lirc-setup-Fix-crash-on-start-on-missing-lirc.config.patch
        0003-python-pkg-Don-t-use-deprecated-time.clock-RHBZ-1718.patch
        0004-database.py-Handle-new-PyYAML-interface.patch
        lirc.logrotate
        lirc.tmpfiles)
sha1sums=('9d6f6d18ac566a96ef4ca1d6909a4e8bc517d48a'
          '952ce902c94d822b0113d8e74bc6fda6a551ad7e'
          'edda4c1f34e7e498fcb14b9a0a54360446883e67'
          '481199646a62553667ce724e37873213e37b0f15'
          'd70dd02731b55ca933cd707759ec703a9e7cc9e7'
          '3d6d40bba363051ae36c09709dc5a41166cf2d51'
          '4342b004eb53d51fcbb9af2cf136bb4990874608'
          '5cd3f206e6e60632d9bea2ce9d22dbe5283eb129')

prepare() {
  cd "${srcdir}/lirc-${_pkgver}"

  patch -p1 -i ../lirc-0.10-build-fix.patch
  patch -p1 -i ../0001-Replace-the-obsolete-get_event_loop-with-get_running.patch
  patch -p1 -i ../0002-lirc-setup-Fix-crash-on-start-on-missing-lirc.config.patch
  patch -p1 -i ../0003-python-pkg-Don-t-use-deprecated-time.clock-RHBZ-1718.patch
  patch -p1 -i ../0004-database.py-Handle-new-PyYAML-interface.patch

  autoreconf -fi
  automake -ac
}

build() {
  cd "${srcdir}/lirc-${_pkgver}"

  ./configure --prefix=/usr \
    --sbindir=/usr/bin \
    --sysconfdir=/etc \
    --localstatedir=/var \
    --enable-devinput \
    --enable-uinput
  sed -i -e 's/ -shared / -Wl,-O1,--as-needed\0/g' libtool
  make
}

package() {
  cd "${srcdir}/lirc-${_pkgver}"

  make DESTDIR="${pkgdir}" -j1 install

  install -Dm644 "${srcdir}"/lirc.tmpfiles "${pkgdir}"/usr/lib/tmpfiles.d/lirc.conf
  install -Dm644 "${srcdir}"/lirc.logrotate "${pkgdir}"/etc/logrotate.d/lirc

  rmdir "${pkgdir}"/var/{run/lirc/,run/}
}
