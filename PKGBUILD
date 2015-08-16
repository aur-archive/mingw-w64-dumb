pkgname=mingw-w64-dumb
pkgver=0.9.3
pkgrel=6
pkgdesc="IT, XM, S3M and MOD player library (mingw-w64)"
arch=(any)
url="http://dumb.sourceforge.net"
license=("custom:dumb, GPL")
makedepends=(mingw-w64-gcc)
depends=(mingw-w64-crt)
options=(!strip !buildflags staticlibs)
source=("http://downloads.sourceforge.net/sourceforge/dumb/dumb-$pkgver.tar.gz"
"CVE-2006-3668.patch"
"config.txt")
sha256sums=('8d44fbc9e57f3bac9f761c3b12ce102d47d717f0dd846657fb988e0bb5d1ea33'
            '9f6785435757db725bc73b1c4874b91e80b9277c6fdd2b56e47dae1cfbc968e6'
            '5319fd08ad12bce4fc3ec41cbeeb71a40041867c56a713d564920f32e0ff4aa2')

_architectures="i686-w64-mingw32 x86_64-w64-mingw32"

prepare() {
	cd "$srcdir/dumb-$pkgver"
  patch -p1 < ../CVE-2006-3668.patch
  cp ../config.txt make/
}

build() {
  for _arch in ${_architectures}; do
    unset LDFLAGS
    mkdir -p "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    cp -r "${srcdir}/dumb-${pkgver}/"* "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    cd "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    sed -i "s,PKGDIR/usr/ARCH,$pkgdir/usr/${_arch}," "make/config.txt"
    make CC=${_arch}-gcc AR=${_arch}-ar RANLIB=${_arch}-ranlib
    ${_arch}-gcc -shared obj/unix/release/*.o -o libdumb.dll -Xlinker --out-implib -Xlinker libdumb.dll.a
  done
}

package() {
  for _arch in ${_architectures}; do
    cd "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    mkdir -p "$pkgdir/usr/${_arch}/"{bin,include,lib}
    make DESTDIR=$pkgdir install
    rm "$pkgdir/usr/${_arch}/lib/libdumbd.a"
    install -m644 "libdumb.dll" "$pkgdir/usr/${_arch}/bin/"
    install -m644 "libdumb.dll.a" "$pkgdir/usr/${_arch}/lib/"
    find "$pkgdir/usr/${_arch}" -name '*.exe' -o -name '*.bat' -o -name '*.def' -o -name '*.exp' | xargs -rtl1 rm
    find "$pkgdir/usr/${_arch}" -name '*.dll' | xargs -rtl1 ${_arch}-strip --strip-unneeded
    find "$pkgdir/usr/${_arch}" -name '*.a' -o -name '*.dll' | xargs -rtl1 ${_arch}-strip -g
  done
}
