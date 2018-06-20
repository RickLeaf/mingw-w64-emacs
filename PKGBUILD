# Maintainer: Haroogan <Haroogan@gmail.com>
# Maintainer: Oscar Fuentes <ofv@wanadoo.es>

_realname=emacs
pkgbase=mingw-w64-${_realname}
pkgname="${MINGW_PACKAGE_PREFIX}-${_realname}"
pkgver=26.1
pkgrel=1
pkgdesc="The extensible, customizable, self-documenting, real-time display editor (mingw-w64)"
url="https://www.gnu.org/software/${_realname}/"
license=('GPL3')
arch=('any')
depends=("${MINGW_PACKAGE_PREFIX}-ctags"
         "${MINGW_PACKAGE_PREFIX}-zlib"
         "${MINGW_PACKAGE_PREFIX}-xpm-nox"
         "${MINGW_PACKAGE_PREFIX}-dbus"
         "${MINGW_PACKAGE_PREFIX}-gnutls"
         "${MINGW_PACKAGE_PREFIX}-imagemagick"
         "${MINGW_PACKAGE_PREFIX}-libwinpthread")
optdepends=("${MINGW_PACKAGE_PREFIX}-giflib"
            "${MINGW_PACKAGE_PREFIX}-libjpeg-turbo"
            "${MINGW_PACKAGE_PREFIX}-libpng"
            "${MINGW_PACKAGE_PREFIX}-librsvg"
            "${MINGW_PACKAGE_PREFIX}-libtiff"
            "${MINGW_PACKAGE_PREFIX}-libxml2")
makedepends=("${MINGW_PACKAGE_PREFIX}-gcc"
             "${MINGW_PACKAGE_PREFIX}-pkg-config"
             "patch")
# Don't zip info files because the built-in info reader uses gzip to
# decompress them. gzip is not available as a mingw binary.
options=('strip' '!zipman')
source=("https://ftp.gnu.org/gnu/${_realname}/${_realname}-${pkgver}.tar.xz"{,.sig}
        'image.c.diff'
        'lread.c.diff'
        'cmigemo.diff'
        'cygwin-rsvg.diff'
        'disable-w32-ime.diff'
        'image-fit.diff'
        'ImmDisableIME.diff'
        'mingw-imagemagick.diff'
        'w32-ime.diff')
sha256sums=('1cf4fc240cd77c25309d15e18593789c8dbfba5c2b44d8f77c886542300fd32c'
            'SKIP'
            '4571d45ec26fd556e73a70bb0ab0a2a8fa1efc5e3b3c5b472ab68bb7dc9bf52c'
            '24b2201aa2e965c759cfc9539aa4b610d97f1d0240d9888e335da490b87b84b5'
            '3c4315a568d8d0a74d76d8d00fd5196d22b9322cef92a82e9783f77b1ea76318'
            '033fae53616b9e24c33e41db95816690077c8f7355b93ccaf3cc1af26914f615'
            '4e4b154285bd324aed8ae54feb993fc56a6eb95fbb38beeec28d3c9703928b18'
            'b44244aa035a53d32f9e954f898d7955bfa37961be3f1989373d11e98a60ce44'
            'd690f8a1656f025390716736b508391e4e1ea4b0e4633a8901689edb60423f61'
            '2d6344e79f20000f21bd0ed329a978e0bcf9130080b444c60b1e60e742f37573'
            '8239a2c0b4452e713f7490210860d8b85d7df99069f8fde0fc9c9df19cbaa702')

prepare() {
  cd "${_realname}-${pkgver}"
  patch --binary --forward -p0 < "${srcdir}/image.c.diff"
  patch --binary --forward -p0 < "${srcdir}/lread.c.diff"
  patch --binary --forward -p0 < "${srcdir}/mingw-imagemagick.diff"
  patch --binary --forward -p0 < "${srcdir}/w32-ime.diff"
  patch --binary --forward -p0 < "${srcdir}/image-fit.diff"
  patch --binary --forward -p0 < "${srcdir}/cmigemo.diff"
  patch --binary --forward -p0 < "${srcdir}/cygwin-rsvg.diff"
  patch --binary --forward -p0 < "${srcdir}/disable-w32-ime.diff"
  patch --binary --forward -p0 < "${srcdir}/ImmDisableIME.diff"
  ./autogen.sh
}

build() {
  [[ -d "${srcdir}/build-${MINGW_CHOST}" ]] && rm -rf "${srcdir}/build-${MINGW_CHOST}"
  mkdir -p "${srcdir}/build-${MINGW_CHOST}"
  cd "build-${MINGW_CHOST}"

  ../${_realname}-${pkgver}/configure \
    --prefix="${MINGW_PREFIX}" \
    --build="${MINGW_CHOST}" \
    --with-modules \
    --without-compress-install

  # --without-compress-install is needed because we don't have gzip in
  # the mingw binaries.

  make
}

package() {
  cd "build-${MINGW_CHOST}"
  make DESTDIR="${pkgdir}" install
  rm -f "${pkgdir}${MINGW_PREFIX}/bin/ctags.exe"
  rm -f "${pkgdir}${MINGW_PREFIX}/share/man/man1/ctags.1.gz"

  local dir="${pkgdir}${MINGW_PREFIX}/share/${_realname}"
        dir="${dir}/$(ls -1 ${dir} | grep -E '([0-9]+\.[0-9]+)(\.[0-9]+)?')/src"

  mkdir -p "${dir}"
  cd "${srcdir}/${_realname}-${pkgver}/src"
  cp *.c *.h *.m "${dir}"
}

# TODO:
# Patch `shell-file-name' default in the C source code similarly to
# `source-directory'.
