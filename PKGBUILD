# Maintainer: George Rawlinson <grawlinson@archlinux.org>
# Contributor: Ronald van Haren <ronald.archlinux.org>
# Contributor : shining <shiningxc.at.gmail.com>
# Contributor : cyberdune <cyberdune@gmail.com>

pkgname=octave
pkgver=8.4.0
pkgrel=3
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
  'arpack'
  'glu'
  'ghostscript'
  'sundials'
  'gl2ps'
  'qscintilla-qt5'
  'libsndfile'
  'qt5-tools'
  'qrupdate'
  'pcre2'
)
makedepends=(
  'gcc-fortran'
  'suitesparse'
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
sha512sums=('d9ebc965c7d4b88128c3cb17c039b224f13ac71542f016eb6811213d65426276013a84b35a6f8ceb84640af1970381b25dadacd521de2faea1696dceea9c99aa'
            'SKIP'
            'f8409113ecb19b1c94d515fbb07cee789b2d792b02c3c12c9796fbaea705cd0472d9210f2e9fe02cc5e525be3f875a885f5de1819d4113c75f4ab25cd0a512f9')

prepare() {
  cd $pkgname-$pkgver
  patch -p1 < ../sundials-7.patch
  autoreconf -vif
}

build() {
  cd "$pkgname-$pkgver"

  # suppress warning message below:
  # egrep: warning: egrep is obsolescent; using grep -E
  export EGREP="grep -E"

  # Workaround build failure with sundials 7
  LDFLAGS+=" -lsundials_core" \
  ./configure \
    --prefix=/usr \
    --libexecdir=/usr/lib \
    --enable-shared \
    --disable-static \
    --with-quantum-depth=16

  sed -i 's/ -shared / -Wl,-O1,--as-needed\0/g' libtool # Fix overlinking
  make
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
