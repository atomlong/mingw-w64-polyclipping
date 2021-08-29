
pkgname=mingw-w64-polyclipping
pkgver=6.4.2
pkgrel=1
pkgdesc="Polygon clipping library (mingw-w64)"
arch=('any')
url="http://sourceforge.net/projects/polyclipping/"
license=('custom')
depends=('mingw-w64-crt')
makedepends=('mingw-w64-cmake' 'dos2unix')
options=('!buildflags' 'staticlibs' '!strip')
source=("http://downloads.sourceforge.net/polyclipping/clipper_ver${pkgver}.zip"
        'https://src.fedoraproject.org/rpms/mingw-polyclipping/raw/rawhide/f/polyclipping.patch')
sha256sums=('a14320d82194807c4480ce59c98aa71cd4175a5156645c4e2b3edd330b930627'
            '83e4884f14e4ab0eb18470407c8881c8cb8721485a4a9ecac0c8e6b1b35bcf03')
_architectures="i686-w64-mingw32 x86_64-w64-mingw32"

prepare() {
  # Correct line ends and encodings
  find . -type f -exec dos2unix -k {} \;

  # fedora patch, see https://sourceforge.net/p/polyclipping/bugs/109/
  patch -p0 -i ../polyclipping.patch

  # install static libs, see #119
  sed -i "s|LIBRARY DESTINATION|ARCHIVE DESTINATION lib LIBRARY DESTINATION|g" cpp/CMakeLists.txt
}

build() {
  cd "$srcdir"
  for _arch in ${_architectures}; do
    unset LDFLAGS
    mkdir -p build-${_arch}-static && pushd build-${_arch}-static
    ${_arch}-cmake \
      -DBUILD_SHARED_LIBS=OFF \
      ../cpp
    make VERBOSE=1
    popd
    mkdir -p build-${_arch} && pushd build-${_arch}
    ${_arch}-cmake \
      ../cpp
    make
    popd
  done
}

package() {
  for _arch in ${_architectures}; do
    cd "$srcdir/build-${_arch}-static"
    make install DESTDIR="$pkgdir"
    cd "$srcdir/build-${_arch}"
    make install DESTDIR="$pkgdir"
    ${_arch}-strip --strip-unneeded "$pkgdir"/usr/${_arch}/bin/*.dll
    ${_arch}-strip -g "$pkgdir"/usr/${_arch}/lib/*.a
  done
}
