# $Id: $
# Maintainer: Tobias Sandmann <tSa *at* gmx *dot* eu>

_pkgname=gcc
pkgname=cross-powerpc-eabi-${_pkgname}-4build
pkgver=4.4.0
pkgrel=1
pkgdesc="Cross compilation tools for PowerPC EABI - The GNU Compiler Collection  [4 build]"
arch=(i686 x86_64)
license=('GPL' 'LGPL')
url="http://gcc.gnu.org/"
depends=('cross-powerpc-eabi-binutils>=2.19' 'mpfr>=2.3.1' 'cloog-ppl>=0.15.3')
makedepends=('flex')
conflicts=('cross-powerpc-eabi-gcc')
options=('!libtool')
source=(ftp://gcc.gnu.org/pub/gcc/releases/${_pkgname}-${pkgver}/${_pkgname}-core-${pkgver}.tar.bz2
	gcc_pure64.patch
	gcc-hash-style-both.patch)
md5sums=('c7e65c47fa94541f7f6cd0cf3d9c850b'
         '4030ee1c08dd1e843c0225b772360e76'
         '6fd395bacbd7b6e47c7b74854b478363')

_prefix=/opt/powerpc-eabi

build() {
  export PATH=$PATH:${_prefix}/bin

  if ! locale -a | grep ^de_DE; then
    echo "You need the de_DE locale to build gcc."
    return 1
  fi
  
  cd ${startdir}/src/${_pkgname}-${pkgver}
  # Don't install libiberty
  sed -i 's/install_to_$(INSTALL_DEST) //' libiberty/Makefile.in

  if [ "${CARCH}" = "x86_64" ]; then
    patch -Np1 -i ../gcc_pure64.patch || return 1
  fi
  patch -Np0 -i ${srcdir}/gcc-hash-style-both.patch || return 1

  echo ${pkgver} > gcc/BASE-VER

  unset CFLAGS
  unset CXXFLAGS
  unset LDFLAGS
  unset MAKEFLAGS

  mkdir ${startdir}/src/build
  cd ${startdir}/src/build

  ${startdir}/src/${_pkgname}-${pkgver}/configure \
    --prefix=${_prefix} \
    --infodir=${_prefix}/info \
    --mandir=${_prefix}/man \
    --target=powerpc-eabi \
    --disable-multilib \
    --disable-nls \
    --enable-shared \
    --with-gcc \
    --with-gnu-as \
    --with-gnu-ld \
    --with-newlib \
    --without-headers \
    || return 1

  make all-gcc || return 1
  make -j1 DESTDIR=${startdir}/pkg install-gcc || return 1

  rm ${startdir}/pkg${_prefix}/info/dir
  gzip -9 ${startdir}/pkg${_prefix}/info/*
}
