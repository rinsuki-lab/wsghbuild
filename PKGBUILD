# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Maintainer: Peter Jung <ptr1337@archlinux.org>
# Contributor: Sven-Hendrik Haase <sh@lutzhaase.com>
# Contributor: Jan "heftig" Steffens <jan.steffens@gmail.com>
# Contributor: Eduardo Romero <eduardo@archlinux.org>
# Contributor: Giovanni Scafora <giovanni@archlinux.org>

pkgname=wine-staging
pkgver=10.7
pkgrel=1

_pkgbasever=${pkgver/rc/-rc}
_winever=$_pkgbasever
#_winever=${_pkgbasever%.*}

source=("git+https://gitlab.winehq.org/wine/wine.git?signed#tag=wine-$_pkgbasever"
        "git+https://gitlab.winehq.org/wine/wine-staging.git#tag=v$_pkgbasever"
        30-win32-aliases.conf
        wine-binfmt.conf
        https://raw.githubusercontent.com/openglfreak/wine-tkg-userpatches/df0a89aec388b23a8a82dca6d53e65d25c3a8f86/patches/9989-misc/ps0071-wdfldr.sys-Initial-implementation.patch
        https://gitlab.winehq.org/wine/wine/-/merge_requests/7959.patch)
sha512sums=('00b283d5ed6497360892b246dd1886a3aadf4098274eb83c66a501bf4b6ff67290b5bebaa8e4c172730dad4aea642468fd3bfda3263f5d27846b15b88afd8bb3'
            '8d69c198229174dc4ca9c93201cab8a718aaf23f01e61b42cf98af25733890aea7995cada054a8a6dc36f6ef3131450d5968157a80b777c69f9c6f8ef97336b0'
            '6e54ece7ec7022b3c9d94ad64bdf1017338da16c618966e8baf398e6f18f80f7b0576edf1d1da47ed77b96d577e4cbb2bb0156b0b11c183a0accf22654b0a2bb'
            'bdde7ae015d8a98ba55e84b86dc05aca1d4f8de85be7e4bd6187054bfe4ac83b5a20538945b63fb073caab78022141e9545685e4e3698c97ff173cf30859e285'
            'SKIP'
            '6aebfc88fc227a1eb00371282833cfaf59c0c7a8c4e59c86ac4fab271c0a18ded75d2f679bf21558fd806ed8bc22060874e84c303d97f10c9fe826f5654381d3')
validpgpkeys=(5AC1A08B03BD7A313E0A955AF5E6E9EEB9461DD7
              DA23579A74D4AD9AF9D3F945CEFAC8EAAF17519D)

pkgdesc="A compatibility layer for running Windows programs - Staging branch"
url="https://www.wine-staging.com"
arch=(x86_64)
options=(staticlibs !lto)
license=(LGPL-2.1-or-later)
depends=(
  attr            #lib32-attr
  desktop-file-utils
  fontconfig      #lib32-fontconfig
  freetype2       #lib32-freetype2
  gcc-libs        #lib32-gcc-libs
  gettext         #lib32-gettext
  libpcap         #lib32-libpcap
  libxcursor      #lib32-libxcursor
  libxi           #lib32-libxi
  libxrandr       #lib32-libxrandr
)
makedepends=(autoconf bison perl flex mingw-w64-gcc
  git
  alsa-lib              #lib32-alsa-lib
  ffmpeg
  giflib                #lib32-giflib
  gnutls                #lib32-gnutls
  gst-plugins-base-libs #lib32-gst-plugins-base-libs
  gtk3                  #lib32-gtk3
  libcups               #lib32-libcups
  libgphoto2
  libpulse              #lib32-libpulse
  libva                 #lib32-libva
  libxcomposite         #lib32-libxcomposite
  libxinerama           #lib32-libxinerama
  libxxf86vm            #lib32-libxxf86vm
  mesa                  #lib32-mesa
  mesa-libgl            #lib32-mesa-libgl
  opencl-headers
  opencl-icd-loader     #lib32-opencl-icd-loader
  samba
  sane
  sdl2                  #lib32-sdl2
  v4l-utils             #lib32-v4l-utils
  vulkan-icd-loader     #lib32-vulkan-icd-loader
)
optdepends=(
  alsa-lib              #lib32-alsa-lib
  alsa-plugins          #lib32-alsa-plugins
  cups                  #lib32-libcups
  dosbox
  ffmpeg
  giflib                #lib32-giflib
  gnutls                #lib32-gnutls
  gst-plugins-base-libs #lib32-gst-plugins-base-libs
  gtk3                  #lib32-gtk3
  libgphoto2
  libpulse              #lib32-libpulse
  libva                 #lib32-libva
  libxcomposite         #lib32-libxcomposite
  libxinerama           #lib32-libxinerama
  opencl-icd-loader     #lib32-opencl-icd-loader
  samba
  sane
  sdl2                  #lib32-sdl2
  v4l-utils             #lib32-v4l-utils
  vulkan-icd-loader     #lib32-vulkan-icd-loader
  wine-gecko
  wine-mono
)

provides=("wine=$pkgver")
conflicts=('wine')
install=wine.install

prepare() {
  # Get rid of old build dirs
  rm -rf $pkgname-{32,64}-build
  mkdir $pkgname-{32,64}-build

  cd wine
  # apply wine-staging patchset
  ../wine-staging/staging/patchinstall.py --backend=git-apply --all
  
  # apply MR 7959 patch
  patch -p1 < ../7959.patch
  patch -p1 < ../ps0071-wdfldr.sys-Initial-implementation.patch
}

build() {
  # Apply flags for cross-compilation
  export CROSSCFLAGS="-O2 -pipe"
  export CROSSCXXFLAGS="-O2 -pipe"
  export CROSSLDFLAGS="-Wl,-O1"

  echo "Building Wine-64..."
  cd "$srcdir/$pkgname-64-build"
  ../wine/configure \
    --prefix=/usr \
    --libdir=/usr/lib \
    --with-x \
    --with-wayland \
    --with-gstreamer \
    --with-xattr \
    --with-freetype \
    --with-ffmpeg \
    --without-gstreamer \
    --enable-archs=x86_64,i386

  make -j$(nproc)
}

package() {
  echo "Packaging Wine-64..."
  cd "$srcdir/$pkgname-64-build"
  make prefix="$pkgdir/usr" \
    libdir="$pkgdir/usr/lib" \
    dlldir="$pkgdir/usr/lib/wine" install

  ln -sf /usr/bin/wine "$pkgdir"/usr/bin/wine64

  # Font aliasing settings for Win32 applications
  install -d "$pkgdir"/usr/share/fontconfig/conf.{avail,default}
  install -m644 "$srcdir/30-win32-aliases.conf" "$pkgdir/usr/share/fontconfig/conf.avail"
  ln -s ../conf.avail/30-win32-aliases.conf "$pkgdir/usr/share/fontconfig/conf.default/30-win32-aliases.conf"
  install -Dm 644 "$srcdir/wine-binfmt.conf" "$pkgdir/usr/lib/binfmt.d/wine.conf"
}

# vim:set ts=8 sts=2 sw=2 et:
