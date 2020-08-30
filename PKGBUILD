pkgname='jasper'
pkgver=2.0.19
pkgrel=1
pkgdesc='Software-based implementation of the codec specified in the emerging JPEG-2000 Part-1 standard'
url='https://www.ece.uvic.ca/~mdadams/jasper/'
arch=('x86_64')
license=('custom:JasPer2.0')
depends=('libjpeg-turbo' 'freeglut' 'glu' 'libxxf86vm')
makedepends=('libxmu' 'cmake' 'doxygen')
options=('staticlibs')
source=(${pkgname}-${pkgver}.tar.gz::https://github.com/mdadams/jasper/archive/version-${pkgver}.tar.gz
        jasper-1.900.1-fix-filename-buffer-overflow.patch)
sha512sums=('b4e1930057587d3417678fc19c661bef21d87a7d6f79fc9486282531097792c4dc074845405c66dc036620344a900349f985a248009934333c5858242ffec655'
            'b8d798bf75523c5db263783e42c653dd0cb03deee90be32eddf878bb6893cca02abadd94de6a8c737a5b7fe76f7fb245979f010765e6a95fc520b215e3a2a7f0')

prepare() {
  cd ${pkgname}-version-${pkgver}
  patch -p1 < "${srcdir}/jasper-1.900.1-fix-filename-buffer-overflow.patch"
  sed -r 's|(CMAKE_SKIP_BUILD_RPATH) FALSE|\1 TRUE|g' -i CMakeLists.txt
  mkdir -p build-shared build-static
}

build() {
  cd ${pkgname}-version-${pkgver}
  local options=(
    -DCMAKE_INSTALL_PREFIX=/usr
    -DCMAKE_INSTALL_LIBDIR=lib
    -DCMAKE_BUILD_TYPE=Release
    -DJAS_ENABLE_OPENGL=ON
    -DJAS_ENABLE_LIBJPEG=ON
    -DJAS_ENABLE_AUTOMATIC_DEPENDENCIES=OFF
    -DCMAKE_SKIP_RPATH=ON
  )
  msg2 "Building static lib..."
  (cd build-static
    cmake ${options[@]} -DJAS_ENABLE_SHARED=OFF ..
    make
  )
  msg2 "Building shared lib..."
  (cd build-shared
    cmake ${options[@]} -DJAS_ENABLE_SHARED=ON ..
    make
  )
}

check() {
  cd ${pkgname}-version-${pkgver}/build-static
  make -j1 test
}

package() {
  cd ${pkgname}-version-${pkgver}
  make -C build-static DESTDIR="${pkgdir}" install
  make -C build-shared DESTDIR="${pkgdir}" install
  install -Dm 644 LICENSE -t "${pkgdir}/usr/share/licenses/${pkgname}"
}
