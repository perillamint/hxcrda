# Maintainer: Somebody <somebody[at]foo[dot]tld>
pkgname=hxcrda
pkgver=99.9
pkgrel=9
pkgdesc="hxcrda is a hacked crda for unleash wifi adaptors potential"
url="http://wireless.kernel.org/en/developers/Regulatory/CRDA"
arch=('x86_64' 'i686' 'arm')
license=('ISC')
depends=('libnl' 'libgcrypt' 'udev' 'iw' 'systemd')
makedepends=('python2-m2crypto' 'git')
conflicts=()
replaces=('crda')
backup=()
install='crda.install'
source=("db.txt"
        "set-wireless-regdom"
        "85-regulatory.rules")
md5sums=('b295ce53a2092072135fc003fbaddfb0'
         '65c93f2ff2eb1b29d9e9fa102ae2dd45'
         '3ab73f8284c24b2418e04abf035b13a8')

_git_regdb="git://git.kernel.org/pub/scm/linux/kernel/git/linville/wireless-regdb.git"
_git_crda="git://git.kernel.org/pub/scm/linux/kernel/git/mcgrof/crda.git"

build() {
  cd "${srcdir}"
  if [[ -d regdb ]]; then
    cd regdb
    git pull
    cd ..
  else
    git clone "${_git_regdb}" regdb
  fi

  if [[ -d crda ]]; then
    cd crda
    git pull
    cd ..
  else
    git clone "${_git_crda}" crda
  fi

  sed -e "s/\#\!\/usr\/bin\/env python.*/\#\!\/usr\/bin\/env python2/g" -i regdb/dbparse.py
  sed -e "s/\#\!\/usr\/bin\/env python.*/\#\!\/usr\/bin\/env python2/g" -i regdb/db2bin.py 
  sed -e "s/\#\!\/usr\/bin\/env python.*/\#\!\/usr\/bin\/env python2/g" -i crda/utils/key2pub.py

  sed -e "s/ldconfig/echo/g" -i regdb/Makefile
  sed -e "s/ldconfig/echo/g" -i crda/Makefile

  cp -f "${srcdir}/db.txt" "${srcdir}/regdb/db.txt"

  cd "${srcdir}/regdb"
  make
  cp -f *.key.pub.pem "${srcdir}/crda/pubkeys/"

  unset LDFLAGS
  unset CFLAGS
  unset CXXFLAGS

  cd "${srcdir}/crda"
  make
}

package() {
  cd "${srcdir}/regdb"
  make DESTDIR="${pkgdir}" PREFIX=/usr/ install

  cd "${srcdir}/crda"
  make DESTDIR="${pkgdir}" PREFIX=/usr/ SBINDIR=/usr/bin/ UDEV_RULE_DIR=/usr/lib/udev/rules.d/ install

  cd "${srcdir}"
  install -Dm644 85-regulatory.rules "${pkgdir}"/usr/lib/udev/rules.d/85-regulatory.rules
  install -Dm755 set-wireless-regdom "${pkgdir}"/usr/bin/set-wireless-regdom
}

# vim:set ts=2 sw=2 et:
