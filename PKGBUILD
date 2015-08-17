# Maintainer: Gustavo Alvarez <sl1pkn07@gmail.com>
# Based on: Anton Shestakov <engored*ya.ru>

_patchlevel=1
_basever=0.148

pkgname=sdlume-wout-gconf
_pkgname=sdlume
pkgver="${_basever}.u${_patchlevel}"
pkgrel=1
pkgdesc="Universal Machine Emulator combines the features of MAME and MESS into a single multi-purpose emulator. Without Gconf and Gtk"
url='http://mamedev.emulab.it/haze/'
license=('custom:MAME License')
arch=('i686' 'x86_64')
provides=('sdlume')
conflicts=('sdlume')
depends=('sdl>=1.2.11' 'fontconfig' 'sdl_ttf' 'alsa-lib')
makedepends=('nasm' 'mesa' 'glu' 'wget' 'python2')
optdepends=('sdlmamefamily-tools: Tools for manage MAME/MESS/UME roms')
DLAGENTS=('http::/usr/bin/wget -U "Mozilla/5.0 (X11; U; Linux x86_64; en-US; rv:1.9.1.2) Gecko/20090804 Shiretoko/3.5.2" -c -t 3 --waitretry=3 -O %o %u')
install="${_pkgname}.install"

for i in $(seq 1 "${_patchlevel}"); do
  _patches="${_patches} sdlmame-${_basever/./}u${i}_diff.zip::http://mamedev.org/updates/${_basever/./}u${i}_diff.zip"
done

source=("mame${_basever/./}s.zip::http://mamedev.org/downloader.php?file=releases/mame${_basever/./}s.zip"
        "sdlume.sh"
        ${_patches}
        'sdlume-wout-gconf-v3.patch')
md5sums=('38f7727c2961cd31e2ab6aa1814a23ba'
         'e1d320d42f62f3a18532640b015ee29a'
         '01edd53824784f52448f4128f6d52aac'
         'd3d87fcab1b0629ba4bc56d305f5843a')

build() {
  cd "${srcdir}"
  bsdtar -xf mame.zip
  find . -type f -not -name "*.png" -exec perl -pi -e 's|\r\n?|\n|g' "{}" \;
  for i in $(seq 1 "${_patchlevel}"); do
    msg "Patch#${i}"
    patch --silent -p0 -E -i "${_basever/./}u${i}.diff"
  done

  msg "Patch disable Gconf and Gtk dependencies"
  patch --silent -p0 -E -i "${srcdir}/sdlume-wout-gconf-v3.patch"
  msg2 "Done"

  make PYTHON=python2 NO_X11=1 OPTIMIZE=2 NOWERROR=1 ARCHOPTS=-march=native FULLNAME=ume TARGET=ume
}

package() {
  cd "${srcdir}"
  # Installing the wrapper script
  install -Dm755 "${srcdir}/${_pkgname}.sh" "${pkgdir}/usr/bin/${_pkgname}"

  # Installing binaries
  install -Dm755 ume "${pkgdir}/usr/share/${_pkgname}/${_pkgname}"

  # Installing extra bits
  install -d "${pkgdir}/usr/share/${_pkgname}/"{artwork,hash,shader,keymaps}
  install -m644 artwork/* "${pkgdir}/usr/share/${_pkgname}/artwork/"
  install -m644 hash/* "${pkgdir}/usr/share/${_pkgname}/hash/"
  install -m644 src/osd/sdl/shader/glsl*.*h "${pkgdir}/usr/share/${_pkgname}/shader/"
  install -m644 src/osd/sdl/keymaps/* "${pkgdir}/usr/share/${_pkgname}/keymaps/"

  # The license
  install -Dm644 docs/license.txt "${pkgdir}/usr/share/licenses/custom/${pkgname}/license.txt"
}
