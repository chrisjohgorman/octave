# Maintainer: Ronald van Haren <ronald.archlinux.org>
# Contributor : shining <shiningxc.at.gmail.com>
# Contributor : cyberdune <cyberdune@gmail.com>

pkgname=octave
pkgver=4.2.2
pkgrel=1
pkgdesc="A high-level language, primarily intended for numerical computations."
arch=('x86_64')
url="http://www.octave.org"
license=('GPL')
depends=('fftw' 'curl' 'graphicsmagick' 'glpk' 'hdf5' 'qhull' 'fltk' 'arpack' 'glu' 'ghostscript'
 	   'suitesparse' 'gl2ps' 'qscintilla-qt5' 'libsndfile')
makedepends=('gcc-fortran' 'texlive-core' 'suitesparse' 'texinfo' 'gnuplot' 'qt5-tools' 'portaudio' 'jdk8-openjdk')
optdepends=('texinfo: for help-support in octave'
            'gnuplot: alternative plotting'
            'portaudio: audio support'
            'java-runtime: java support')
source=(ftp://ftp.gnu.org/gnu/octave/octave-$pkgver.tar.gz{,.sig})
options=('!emptydirs')
validpgpkeys=('DBD9C84E39FE1AAE99F04446B05F05B75D36644B')  # John W. Eaton
sha512sums=('b94edd79adc0e19229bb654037910201b51b6cfa373d63de5e3aa69e9b659b2e2790e2d2b4b5e8d2f12b26846c20ba5c12eae657155c8329e85e970f738d08c2'
            'SKIP')


build() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  ./configure --prefix=/usr --libexecdir=/usr/lib \
  --enable-shared --disable-static \
  --with-quantum-depth=16 \
  --with-umfpack="-lumfpack -lsuitesparseconfig"
# https://mailman.cae.wisc.edu/pipermail/help-octave/2012-September/053991.html 

  LANG=C make
}

package(){
  cd "${srcdir}/${pkgname}-${pkgver}"

  make DESTDIR="${pkgdir}" install

  # add octave library path to ld.so.conf.d
  install -d "${pkgdir}/etc/ld.so.conf.d"
  echo "/usr/lib/${pkgname}/${pkgver}" > "${pkgdir}/etc/ld.so.conf.d/${pkgname}.conf"
}
