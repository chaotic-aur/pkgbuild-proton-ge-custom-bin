## Maintainer:     barfin <barfin@protonmail.com>
## Co-Maintainer:  Jaja <jaja@mailbox.org>
## Co-Maintainer:  floriplum <floriplum@mailbox.org>

## Linted using:
##   $ shellcheck PKGBUILD -e SC2034,SC2148,SC2154
## Formated using:
##   $ shfmt -w PKGBUILD

## pkginfo
pkgdesc='A fancy custom distribution of Valves Proton with various patches'
pkgname=proton-ge-custom-bin
pkgver=GE_Proton7_20
pkgrel=2
epoch=1
arch=('x86_64')
license=('BSD' 'LGPL' 'zlib' 'MIT' 'MPL' 'custom')
changelog=changelog.md
provides=('proton' "proton-ge-custom=${pkgver/_/.}")
conflicts=('proton-ge-custom')

## dependencies
depends=('python'
	'vulkan-icd-loader'
	'lib32-openal'
	# libav support #
	'lib32-libva'
	'ffmpeg4.4'
	'lib32-speex'
	'lib32-libtheora'
	'lib32-libvdpau'
	# gstreamer support #
	'gst-plugins-bad-libs'
	'lib32-gst-plugins-base-libs'
	'libjpeg6-turbo'
	'lib32-libjpeg6-turbo'
	'lib32-libgudev'
	'lib32-flac'
	'lib32-mpg123'
	'lib32-libusb')
optdepends=('kdialog: KDE splash dialog support'
	'zenity: GNOME splash dialog support'
	'python-kivy: splash dialog support (big picture mode)'
	'steam: use proton with steam like intended'
	'lib32-vulkan-icd-loader: dxvk dependency for 32bit prefixes'
	'vulkan-driver: driver to be used by dxvk'
	'winetricks: protonfixes backend - highly recommended'
	'wine: support for 32bit prefixes'
	'xboxdrv: gamepad driver service')

## makepkg options
options=(!strip emptydirs)
install=pleasenote.install

## fix naming conventions, matching upstream
_pkgname=${pkgname//-bin/}
_pkgver=${pkgver//_/-}
_srcdir=${_pkgver}

## paths and files
_protondir=usr/share/steam/compatibilitytools.d/${_pkgname}
_licensedir=usr/share/licenses/${pkgname}
_execfile=usr/bin/proton
_protoncfg=${_protondir}/user_settings.py

## user edited files to backup
backup=("${_protoncfg}")

## sources
url='https://github.com/GloriousEggroll/proton-ge-custom'
source=("${_pkgver}_${pkgrel}.tar.gz::${url}/releases/download/${_pkgver}/${_pkgver}.tar.gz"
	'user_settings.py'
	'launcher.sh'
	'pam_limits.conf')
sha512sums=('b3e88e4013b725ad90a3178e064cf038bd2b2afac30acaf963ff3d5240a68755700c26afb61393b71e34d51d5cef330a180206d4ac97cc603305e64c2b1cf7db'
            'cd70fa35e8565197148c6135628ea4c751c7dc4d7eba6e59cf8a8f2315e79f45e80fc3adce68c8ca2c195a18aaa2a8b2b346e8843b369f3d0ac97e752dbb5399'
            '33efb407e47140a72f1024bec67f2d718eec56e13ca76bcc18e03471b2c64f2b04034eb1e20b0da79afb727e59672fd3539fecc8131da88a8a1330f48a1c8424'
            'c64898bd41801470925fb0efdcf7d247e5cb476fb4745f83ceeccf12041474e5c309fb1c2ac1483b419d12b4ade7668c046bebded4e3bf4708737ee505b080a1')

build() {
	## patches
	sed -i "s|_proton=echo|_proton=/${_protondir}/proton|" "${srcdir}"/launcher.sh
	sed -i -r 's|"GE-Proton.*"|"Proton-GE"|' "${_srcdir}"/compatibilitytool.vdf
	## remove artifacts
	rm "${_srcdir}"/protonfixes/*.tar.xz
	rm -rf "${_srcdir}"/protonfixes/.git*
	## fixes from namcap inspection
	strip --preserve-dates --strip-unneeded "${_srcdir}"/files/bin/wine*
}

package() {
	## create paths
	install -d "${pkgdir}/${_protondir}/"
	install -d "${pkgdir}/${_licensedir}/"
	install -d "${pkgdir}/$(dirname ${_execfile})/"
	install -d "${pkgdir}/etc/security/limits.d/"
	## licenses
	mv "${_srcdir}/LICENSE" "${pkgdir}/${_licensedir}/license"
	mv "${_srcdir}/LICENSE.OFL" "${pkgdir}/${_licensedir}/license_OFL"
	mv "${_srcdir}/PATENTS.AV1" "${pkgdir}/${_licensedir}/license_AV1"
	mv "${_srcdir}/protonfixes/LICENSE" "${pkgdir}/${_licensedir}/license_protonfixes"
	## config files
	install --mode=0775 --group=50 "${srcdir}"/user_settings.py "${pkgdir}/${_protoncfg}"
	install --mode=0644 "${srcdir}"/pam_limits.conf "${pkgdir}"/etc/security/limits.d/10-games.conf
	## executables
	mv "${_srcdir}"/* "${pkgdir}/${_protondir}"
	install --mode=0755 "${srcdir}"/launcher.sh "${pkgdir}/${_execfile}"
}
