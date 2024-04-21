# Maintainer: George Rawlinson <grawlinson@archlinux.org>
# Contributor: Ronald van Haren <ronald.archlinux.org>
# Contributor : shining <shiningxc.at.gmail.com>
# Contributor : cyberdune <cyberdune@gmail.com>
# Contributor : Chris Gorman <chrisjohgorman@gmail.com>

pkgname=octave
pkgver=9.1.0
pkgrel=1
pkgdesc='A high-level language, primarily intended for numerical computations'
arch=('x86_64')
url='https://www.gnu.org/software/octave/'
license=('GPL')
depends=(
  'fftw'
  'curl'
  'graphicsmagick'
  'glpk'
  'hdf5'
  'qhull'
  'arpack64'
  'glu'
  'ghostscript'
  'sundials64'
  'gl2ps'
  'qscintilla-qt6'
  'libsndfile'
  'qt6-tools'
  'qrupdate64'
  'pcre2'
)
makedepends=(
  'gcc-fortran'
  'suitesparse64'
  'texinfo'
  'gnuplot'
  'fltk'
  'portaudio'
  'jdk-openjdk'
  'rapidjson'
)
optdepends=(
  'texinfo: for help-support in octave'
  'gnuplot: alternative plotting'
  'portaudio: audio support'
  'java-runtime: java support'
  'fltk: FLTK GUI'
  'texlive-bin: for the publish command'
)
source=("https://ftp.gnu.org/gnu/octave/octave-$pkgver.tar.gz"{,.sig}
         sundials-7.patch)
options=('!emptydirs')
validpgpkeys=('DBD9C84E39FE1AAE99F04446B05F05B75D36644B')  # John W. Eaton
sha512sums=('1b4370ce0970ce360c91b054b79d9b0dd00715a2384bf7aefd2b4e851cbea836c7bfe4d801543056070d1d4ccb2f3ce85118959568df76a6bff2694ea50a3ba8'
            'SKIP'
            'f8409113ecb19b1c94d515fbb07cee789b2d792b02c3c12c9796fbaea705cd0472d9210f2e9fe02cc5e525be3f875a885f5de1819d4113c75f4ab25cd0a512f9')

build() {
  cd "$pkgname-$pkgver"

  # suppress warning message below:
  # egrep: warning: egrep is obsolescent; using grep -E
  export EGREP="grep -E"

  JAVA_HOME=/usr/lib/jvm/default \
  ./configure \
    --enable-64 \
    --with-blas=/usr/lib/libblas64.so \
    --with-lapack=/usr/lib/liblapack64.so \
    --prefix=/usr \
    --libexecdir=/usr/lib \
    --enable-shared \
    --disable-static \
    --with-amd-includedir=/usr/include/suitesparse64 \
    --with-amd=-lamd64 \
    --with-arpack-includedir=/usr/include/arpack64 \
    --with-arpack=-larpack64 \
    --with-camd-includedir=/usr/include/suitesparse64 \
    --with-camd=-lcamd64 \
    --with-ccolamd-includedir=/usr/include/suitesparse64 \
    --with-ccolamd=-lccolamd64 \
    --with-cholmod-includedir=/usr/include/suitesparse64 \
    --with-cholmod=-lcholmod64 \
    --with-colamd-includedir=/usr/include/suitesparse64 \
    --with-colamd=-lcolamd64 \
    --with-cxsparse-includedir=/usr/include/suitesparse64 \
    --with-cxsparse=-lcxsparse64 \
    --with-qrupdate-includedir=/usr/include/openblas64 \
    --with-qrupdate="-lqrupdate64 -lopenblas_64" \
    --with-spqr-includedir=/usr/include/suitesparse64 \
    --with-spqr=-lspqr64 \
    --with-suitesparseconfig=-lsuitesparseconfig64 \
    --with-umfpack-includedir=/usr/include/suitesparse64 \
    --with-umfpack=-lumfpack64

  sed -i 's/ -shared / -Wl,-O1,--as-needed\0/g' libtool # Fix overlinking
  make -j6
}

package(){
  cd "$pkgname-$pkgver"

  make DESTDIR="$pkgdir" install

  # add octave library path to ld.so.conf.d
  install -d "$pkgdir/etc/ld.so.conf.d"
  echo "/usr/lib/$pkgname/$pkgver" > "$pkgdir/etc/ld.so.conf.d/$pkgname.conf"

  # dirty hack to make package reproducible
  local ARCHIVE_DATE="$(TZ=UTC date --reference=ChangeLog --iso-8601=seconds)"
  mkdir tmpdir
  cd tmpdir
  jar --extract --file="$pkgdir/usr/share/octave/$pkgver/m/java/octave.jar"
  rm -rf "$pkgdir/usr/share/octave/$pkgver/m/java/octave.jar"
  jar --create --date="$ARCHIVE_DATE" --file="$pkgdir/usr/share/octave/$pkgver/m/java/octave.jar" ./*
}
