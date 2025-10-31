# Maintainer: Alexis "Horgix" Chotard <aur-murmure@foss.horgix.fr>

pkgname=murmure
pkgver=1.3.0
pkgrel=1
pkgdesc="Privacy-first and free Speech-to-Text"
license=('GPL-3.0-only')
url='https://murmure.al1x-ai.com/'
arch=('x86_64')
_appimg="${pkgname}-${pkgver}.AppImage"

# The code used for the build is the one for GitHub
# The AppImage is only here for the Parakeet ONNX model that is embedded into it
source=(
  "${pkgname}-${pkgver}.tar.gz::https://github.com/Kieirra/${pkgname}/archive/refs/tags/${pkgver}.tar.gz"
  "${_appimg}::https://github.com/Kieirra/${pkgname}/releases/download/${pkgver}/${pkgname}_${pkgver}_amd64.AppImage"
)
sha512sums=(
  'ceb23514c30508614793c5cb53116141e1884903c0c9fc1ff5ee9356f5b29eed9c3c03ad11d984a8b48ee489e926ecb532200265e2a74f9773a82f862562ecc7'
  'a6e0b9169dc2cc0e1972ba45e942d823de197a5f9bd2176915562856eede040da8a81b88465d90567054985a8b1bdf6722c214ab7856a34f2b8f73dbda5bff83')



depends=('cairo' 'desktop-file-utils' 'gdk-pixbuf2' 'glib2' 'gtk3' 'hicolor-icon-theme' 'libsoup' 'pango' 'webkit2gtk-4.1')
makedepends=('cargo' 'pnpm' 'nodejs')
provides=('murmure')

prepare() {
  # Model extraction
  chmod +x "${_appimg}"
  "./${_appimg}" --appimage-extract

  # Dependencies install from source
  cd ${pkgname}-${pkgver}/
  pnpm install
}

build() {
  cd ${pkgname}-${pkgver}/
  CFLAGS+=' -ffat-lto-objects'
  pnpm tauri build -b deb
}

package() {
  cd ${pkgname}-${pkgver}/
  ls
  tree src-tauri/target/release
  cp -a src-tauri/target/release/bundle/deb/${pkgname}_${pkgver}_amd64/data/* "${pkgdir}"
  cd ..

  # The directory content is only Git LFS pointer files, not the actual models
  rm -r ${pkgdir}/usr/lib/murmure/_up_/resources/parakeet-tdt-0.6b-v3-int8
  # Take them from the AppImage instead
  cp -ar "squashfs-root/usr/resources/parakeet-tdt-0.6b-v3-int8" "${pkgdir}/usr/lib/murmure/_up_/resources/"
}
