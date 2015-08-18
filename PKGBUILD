# Maintainer: BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributors : Ralf Barth <archlinux dot org at haggy dot org>
#
# Original credits go to Edgar Hucek <gimli at dark-green dot com>
# for his xbmc-vdpau-vdr PKGBUILD at https://archvdr.svn.sourceforge.net/svnroot/archvdr/trunk/archvdr/xbmc-vdpau-vdr/PKGBUILD

pkgname=xbmc-eden-git
pkgver=20121104
pkgrel=1
pkgdesc="XBMC Media Center git Eden branch"
provides=('xbmc')
conflicts=('xbmc' 'xbmc-pulse' 'xbmc-svn' 'xbmc-git')
replaces=('xbmc-svn')
arch=('i686' 'x86_64')
url="http://xbmc.org"
license=('GPL' 'LGPL')
depends=('libpulse' 'hicolor-icon-theme' 'fribidi' 'lzo2' 'smbclient' 'libtiff' 'libva' 'libpng' 'libcdio' 'yajl' 'libmysqlclient' 'libjpeg-turbo' 'libsamplerate' 'glew' 'libssh' 'libmicrohttpd' 'libxrandr' 'sdl_mixer' 'sdl_image' 'python2' 'libass' 'libmpeg2' 'libmad' 'libmodplug' 'jasper' 'rtmpdump' 'unzip' 'mesa-demos' 'xorg-xdpyinfo' 'libbluray' 'libnfs' 'afpfs-ng' 'libshairport' 'avahi' 'bluez' 'tinyxml' 'glu' 'mesa')
makedepends=('boost' 'cmake' 'git' 'gperf' 'nasm' 'libxinerama' 'zip' 'libva-vdpau-driver' 'libcec' 'udisks' 'upower')

optdepends=(
            'gdb: for meaningful backtraces in case of trouble - STRONGLY RECOMMENDED'
            'libcrystalhd: Broadcom CrystalHD kernel module'
			'libcec: support for Pulse-Eight USB-CEC adapter'
			'libva-vdpau-driver: accelerated video playback for nvidia cards'
            'xvba-video: accelerated video playback for amd cards'
            'pulseaudio: pulseaudio support'
            'lirc: remote controller support'
            'udisks: automount external drives'
            'upower: used to trigger suspend functionality'
            'unrar: access compressed files without unpacking them'
)
install="$pkgname.install"
source=()
sha256sums=()

_prefix='/usr'

if [ -e .githash_$CARCH ] ; then
	_gitphash=$(cat .githash_$CARCH)
else
	_gitphash=""
fi

_gitname='xbmc'
_gitroot='git://github.com/xbmc/xbmc.git'

build() {
	if [ -d "$srcdir/$_gitname/.git" ] ; then
		( cd "$srcdir/$_gitname" && git pull origin Eden )
		msg "The local files are updated."
	else
		( git clone -b Eden --depth 1  $_gitroot $_gitname )
	fi
	msg "GIT checkout done or server timeout"

	cd $_gitname
	if [ "$_gitphash" = "$(git show | grep -m 1 commit | sed 's/commit //')" ]; then
		msg "Git hash is the same as previous build"
		return 1
	fi

	msg "creating build directory"
	cd "$srcdir"
	[ -d $_gitname-build ] && rm -rf $_gitname-build
	cp -a $_gitname $_gitname-build

	msg "Starting make..."
	cd $_gitname-build

	# Configure XBMC
	#
	# Note on external-libs:
	#   - We cannot use external python because Arch's python was built with
	#     UCS2 unicode support, whereas xbmc expects UCS4 support
	#   - According to an xbmc dev using external/system ffmpeg with xbmc is "pure stupid" :D

    # fix lsb_release dependency
    sed -i -e 's:/usr/bin/lsb_release -d:cat /etc/arch-release:' xbmc/utils/SystemInfo.cpp

	msg2 "Bootstrapping XBMC"
	./bootstrap

	# latest configure options
	# --enable-neon           enable neon passing to ffmpeg (default is no)
	# --enable-optimizations  enable optimization (default is yes)
	# --enable-gl             enable OpenGL rendering (default is yes)
	# --enable-gles           enable OpenGLES rendering (default is no)
	# --enable-sdl            enable SDL (default is auto)
	# --enable-vdpau          enable VDPAU decoding (default is auto)
	# --enable-vaapi          enable VAAPI decoding (default is auto)
	# --enable-crystalhd      enable CrystalHD decoding (default is auto)
	# --enable-vdadecoder     enable VDADecoder decoding (default is auto)
	# --enable-vtbdecoder     enable VTBDecoder decoding (default is auto)
	# --enable-openmax        enable OpenMax decoding (default is auto, requires OpenGLES)
	# --enable-tegra          enable Tegra2 arm (default is no)
	# --enable-profiling      enable gprof profiling (default is no)
	# --enable-joystick       enable SDL joystick support (default is yes)
	# --enable-xrandr         enable XRandR support (default is yes)
	# --enable-goom           enable GOOM visualisation (default is no)
	# --enable-rsxs           enable really slick X screensavers (default is yes)
	# --enable-projectm       enable ProjectM visualisation (default is yes)
	# --enable-x11            enable x11 (default is yes) 'Linux Only'
	# --enable-ccache         enable building with ccache feature (default is auto)
	# --enable-pulse          enable PulseAudio support (default is auto)
	# --enable-rtmp           enable RTMP support via librtmp (default is auto)
	# --disable-samba         disable SAMBA support (default is enabled)
	# --enable-nfs            enable NFS support via libnfs (default is auto)
	# --enable-afpclient      enable AFP support via libafpclient (default is auto)
	# --enable-airplay        enable AirPlay support(default is auto)
	# --enable-airtunes       enable AirTunes support(default is auto)
	# --enable-ffmpeg-libvorbis enable FFmpeg vorbis encoding (default is no)
	# --enable-dvdcss         enable DVDCSS support (default is yes)
	# --enable-mid            enable MID support (default is no)
	# --disable-hal           disable HAL support (default is enabled if hal and hal-storage is found)
	# --disable-avahi         disable Avahi support (default is enabled if libavahi-common and libavahi-client is found)
	# --disable-non-free      disable componentents with non-compliant licenses
	# --enable-asap-codec     enable ASAP ADPCM support
	# --disable-webserver     disable webserver
	# --disable-optical-drive disable optical drive
	# --enable-libbluray      enable libbluray support
	# --enable-texturepacker  enable texturepacker support (default is yes)
	# --enable-udev           enable udev support (default is auto)
	# --enable-libusb         enable libusb support (default is auto)
	# --enable-libcec         enable libcec support (default is auto)
	# --enable-external-libraries enable use of all supported external libraries (default is no) 'Linux only'
	# --enable-external-ffmpeg enable use of external ffmpeg libraries (default is no) 'Linux only'

	msg2 "Configuring XBMC"
    export PYTHON_VERSION=2  # external python v2
	./configure --prefix=$_prefix --exec-prefix=$_prefix \
		--enable-debug \
		--enable-optimizations \
		--enable-gl \
		--enable-sdl \
		--enable-vaapi \
		--enable-vdpau \
		--enable-xvba \
		--enable-joystick \
		--enable-xrandr \
		--enable-rsxs \
		--enable-projectm \
		--enable-x11 \
		--enable-pulse \
		--enable-rtmp \
		--enable-samba \
		--enable-nfs \
		--enable-afpclient \
		--enable-airplay \
		--enable-airtunes \
		--enable-ffmpeg-libvorbis \
		--enable-dvdcss \
		--disable-hal \
		--enable-avahi \
		--enable-webserver \
		--enable-optical-drive \
		--enable-libbluray \
		--enable-texturepacker \
		--enable-udev \
		--enable-libusb \
		--enable-libcec \
		--enable-external-libraries \
		--disable-external-ffmpeg

	# Now (finally) build
	msg2 "Running make"
	make

	# make eventclients
	#cd tools/EventClients
	#make
}

package() {
	cd ${_gitname}-build
	msg2 "Running make install"
	make DESTDIR="$pkgdir" install

	# run feh with python2
    sed -i -e 's/python/python2/g' ${pkgdir}${_prefix}/bin/xbmc

	# Tools
	msg2 "Tools"
	install -Dm755 $srcdir/$_gitname-build/tools/TexturePacker/TexturePacker \
		${pkgdir}${_prefix}/lib/xbmc/

	#(
		#cd tools/EventClients
		#make DESTDIR="$pkgdir" install
	#)

	# Licenses
	msg2 "Copy licenses"
	install -dm755 ${pkgdir}${_prefix}/share/licenses/${pkgname}
	for licensef in LICENSE.GPL copying.txt; do
		mv ${pkgdir}${_prefix}/share/doc/xbmc/${licensef} \
			${pkgdir}${_prefix}/share/licenses/${pkgname}
	done

	git show | grep -m 1 commit | sed 's/commit //' > $startdir/.githash_$CARCH
}
