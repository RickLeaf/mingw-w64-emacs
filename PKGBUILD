# Maintainer: Haroogan <Haroogan@gmail.com>
# Maintainer: Oscar Fuentes <ofv@wanadoo.es>

_realname=emacs
pkgbase=mingw-w64-${_realname}
pkgname="${MINGW_PACKAGE_PREFIX}-${_realname}"
pkgver=25.2
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
        'emacs-25.2-cmigemo.diff'
        'emacs-25.2-cygwin-rsvg.diff'
        'emacs-25.2-disable-w32-ime.diff'
        'emacs-25.2-image-fit.diff'
        'emacs-25.2-ImmDisableIME.diff'
        'emacs-25.2-mingw-imagemagick.diff'
        'emacs-25.2-w32-ime.diff'
        )
sha256sums=('59b55194c9979987c5e9f1a1a4ab5406714e80ffcfd415cc6b9222413bc073fa'
            'SKIP'
            '4571d45ec26fd556e73a70bb0ab0a2a8fa1efc5e3b3c5b472ab68bb7dc9bf52c'
            '24b2201aa2e965c759cfc9539aa4b610d97f1d0240d9888e335da490b87b84b5'
            '351a59dcda54a2971b12a9ae47033e2d8e4312d4ddca5029f952718ddcfff9af'
            'f3cef8a7bd94bcbc96e7e969d894456255def79e0b573666129cf54f121e20d5'
            '4e4b154285bd324aed8ae54feb993fc56a6eb95fbb38beeec28d3c9703928b18'
            'b62774d6411d8cf6ba6f73b0f5ef51804f7b387305c740b093db6cb1fc76139f'
            'd690f8a1656f025390716736b508391e4e1ea4b0e4633a8901689edb60423f61'
            '2d6344e79f20000f21bd0ed329a978e0bcf9130080b444c60b1e60e742f37573'
            '71b4c1320420a083271d930a973e232a1f5f9ca6f6ff4c3e14be6ca0c0d82d8d')

prepare() {
  cd "${_realname}-${pkgver}"
  #patch --binary --forward -p0 < "${srcdir}/image.c.diff"
  patch --binary --forward -p0 < "${srcdir}/lread.c.diff"
  patch --binary --forward -p0 < "${srcdir}/emacs-25.2-mingw-imagemagick.diff"
  patch --binary --forward -p0 < "${srcdir}/emacs-25.2-w32-ime.diff"
  patch --binary --forward -p0 < "${srcdir}/emacs-25.2-image-fit.diff"
  patch --binary --forward -p0 < "${srcdir}/emacs-25.2-cmigemo.diff"
  patch --binary --forward -p0 < "${srcdir}/emacs-25.2-cygwin-rsvg.diff"
  patch --binary --forward -p0 < "${srcdir}/emacs-25.2-disable-w32-ime.diff"
  patch --binary --forward -p0 < "${srcdir}/emacs-25.2-ImmDisableIME.diff"
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
