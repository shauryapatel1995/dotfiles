# Maintainer: Cody Schafer <aur at codyps com>
# Maintainer: dequis <dx@dxzone.com.ar>
# Previous maintainer: Joel Teichroeb <joel@teichroeb.net>

pkgname=rr
pkgver=5.4.0
pkgrel=5
pkgdesc='Record and Replay framework: lightweight recording and deterministic debugging'
arch=(i686 x86_64)
url='http://rr-project.org/'
license=('custom')
depends=('gdb' 'capnproto')
makedepends=('git' 'cmake' 'gdb' 'ninja')
options=(!strip)

source=(
	$pkgname-$pkgver.tar.gz::https://github.com/rr-debugger/${pkgname}/archive/${pkgver}.tar.gz
)
sha1sums=('b4716dd10b01ae4b8d35ae08f4f67fc46f0693d9')

prepare() {
	cd $pkgname-$pkgver
	mkdir -p build
}

build() {
	cd $pkgname-$pkgver/build
	cmake \
		-GNinja \
		-Ddisable32bit=true \
		-DCMAKE_BUILD_TYPE=plain \
		-DCMAKE_INSTALL_PREFIX:PATH=/usr \
		-DBUILD_TESTS=OFF \
		-DWILL_RUN_TESTS=OFF \
		-DCMAKE_INSTALL_LIBDIR=lib \
		-DCMAKE_CXX_STANDARD=14 \
		..

	cmake --build .	 -- -v
}

package() {
	cd $pkgname-$pkgver/build
	DESTDIR="${pkgdir}" cmake --build . -- -v install
	if check_option 'debug' n; then
		find "${pkgdir}/usr/bin" -type f -executable -exec strip $STRIP_BINARIES {} + || :
	fi
	cd ..
	install -D LICENSE "${pkgdir}/usr/share/licenses/rr/LICENSE"
}
