# Maintainer:  danyf90 <daniele.formichelli@gmail.com>
# Contributor: Mitch Bigelow <ipha00@gmail.com>

pkgname=gwoffice-bzr
pkgver=106
pkgrel=1
pkgdesc="Google Web Office - GWoffice - brings your google drive to your dektop"
arch=('i686' 'x86_64')
url="https://launchpad.net/gwoffice"
license=('GPL3')
depends=('granite-bzr' 'libgnome-keyring' 'libwebkit3' 'gnome-online-accounts' 'libgdata')
makedepends=('bzr' 'cmake' 'vala')
install=$pkgname.install

_bzrtrunk=lp:gwoffice
_bzrmod=gwoffice

build() {
	msg "Connecting to Bazaar server...."

	if [[ -d $_bzrmod ]]; then
		cd $_bzrmod && bzr pull $_bzrtrunk -r $pkgver
		msg "Local files updated."
	else
		bzr branch $_bzrtrunk $_bzrmod -r $pkgver
	fi

	msg "Bazaar checkout done or server timeout"
	msg "Starting build..."

	[ -d $srcdir/$_bzrmod-build ] && rm -rf $srcdir/$_bzrmod-build
	cp -r $srcdir/$_bzrmod $srcdir/$_bzrmod-build
	cd $srcdir/$_bzrmod-build

	# Strip out unity support
	sed -i 's/unity//' CMakeLists.txt

	sed -i 's/public Unity.LauncherEntry launcher;//' src/Widgets/WebView.vala
	sed -i 's/Unity.LauncherEntry _launcher,//' src/Widgets/WebView.vala
	sed -i 's/^\s*launcher.*$//' src/Widgets/WebView.vala

	sed -i 's/public Dbusmenu.Menuitem menuitem;//' src/Widgets/WebView.vala
	sed -i '/^\s*menuitem/,/});/d' src/Widgets/WebView.vala


	sed -i 's/public Unity.LauncherEntry launcher;//' src/GWoffice.vala
	sed -i 's/, launcher//' src/GWoffice.vala
	sed -i 's/^\s*launcher.*$//' src/GWoffice.vala

	sed -i 's/.*launcher.*//' src/Services/Sync.vala

	# build
	[ -d build ] && rm -rf build
	mkdir build
	cd build
	cmake .. -DCMAKE_INSTALL_PREFIX=/usr -DCMAKE_EXE_LINKER_FLAGS=-loauth -DGSETTINGS_COMPILE=off
	make
}

package() {
	cd $srcdir/$_bzrmod-build/build
	make DESTDIR=$pkgdir install
	install -d $pkgdir/usr/bin
	ln -s /opt/extras.ubuntu.com/gwoffice/bin/gwoffice $pkgdir/usr/bin/
}
