# Maintainer: Alexander F. Rødseth <xyproto@archlinux.org>
# Contributor: loqs
# Contributor: Jorge Araya Navarro <jorgejavieran@yahoo.com.mx>
# Contributor: Cristian Porras <porrascristian@gmail.com>
# Contributor: Matthew Bentley <matthew@mtbentley.us>
# Contributor: HurricanePootis <hurricanepootis@protonmail.com>
# Contributor: Toolybird <toolybird at tuta dot io>

pkgname=godot
pkgver=4.6
pkgrel=1
pkgdesc='Advanced cross-platform 2D and 3D game engine'
url='https://godotengine.org/'
license=(MIT)
arch=(x86_64)
makedepends=(alsa-lib pulse-native-provider scons setconf yasm)
depends=(brotli ca-certificates embree freetype2 graphite libglvnd libspeechd libsquish libtheora libvorbis
  libwebp libwslay libxcursor libxi libxinerama libxrandr miniupnpc openxr pcre2)
optdepends=('pipewire-alsa: for audio support'
  'pulse-native-provider: for audio support')
source=("$pkgname-$pkgver.tar.xz::https://github.com/godotengine/godot/releases/download/$pkgver-stable/$pkgname-$pkgver-stable.tar.xz"
        "enable-sse42-toggle.patch")
b2sums=('a3f40dc0ad08733fa0e5eb1044e0c8075df6147592493593e70d4742f36a9f2e22a70f655cbb083509ce45b7767008d8786f1f3d01c5ff5d35761b390134e8fd'
        '3bf60da7a685048529596b563c98fe1a53c1d525262e2a2dc38337847b85143205191aee85f2cd28be68b28d2075de59658605cbd9b0b34bf0d65d191d9bfe67')

prepare() {
  cd $pkgname-$pkgver-stable

  # Patch for miniupnpc
  sed -i 's/addr, 16/addr, 16, nullptr, 0/g' modules/upnp/upnp.cpp

  cd misc/dist/linux

  # MIME info fix, ref FS#77810
  sed -i 's,xmlns="https://specifications.freedesktop.org/shared-mime-info-spec",xmlns="http://www.freedesktop.org/standards/shared-mime-info",g' \
    org.godotengine.Godot.xml

  patch -Np1 -i "../enable-sse42-toggle.patch"
}

case $CARCH in
x86_64*) _CARCH=x86_64 ;;
aarch64) _CARCH=arm64 ;;
esac
build() {
  cd $pkgname-$pkgver-stable

  export BUILD_NAME=arch_linux

  # Not unbundled (yet):
  #  mbedtls
  #  enet (contains no upstreamed IPv6 support)
  #  AUR: libwebm, rvo2
  #  recastnavigation, xatlas

  _args=(
    -j$(nproc --all)
    cflags="$CFLAGS -march=x86-64 -mtune=generic -fPIC -Wl,-z,relro,-z,now -w"
    cxxflags="$CXXFLAGS -march=x86-64 -mtune=generic -fPIC -Wl,-z,relro,-z,now -w"
    linkflags="$LDFLAGS"
    arch=$_CARCH
    builtin_brotli=no
    builtin_certs=no
    builtin_clipper2=yes
    builtin_embree=no
    builtin_enet=yes
    builtin_freetype=no
    builtin_glslang=yes
    builtin_graphite=no
    builtin_harfbuzz=yes
    builtin_icu4c=yes
    builtin_libogg=no
    builtin_libpng=no
    builtin_libtheora=no
    builtin_libvorbis=no
    builtin_libwebp=no
    builtin_mbedtls=yes
    builtin_miniupnpc=no
    builtin_msdfgen=yes
    builtin_openxr=no
    builtin_pcre2=no
    builtin_pcre2_with_jit=no
    builtin_recastnavigation=yes
    builtin_rvo2_2d=yes
    builtin_rvo2_3d=yes
    builtin_squish=no
    builtin_wslay=yes
    builtin_xatlas=yes
    builtin_zlib=no
    builtin_zstd=no
    colored=yes
    debug_symbols=no
    disable_exceptions=false
    platform=linuxbsd
    production=yes
    pulseaudio=yes
    system_certs_path=/etc/ssl/certs/ca-certificates.crt
    enable_sse42=no
    target=editor
    use_llvm=no
    #linker=lld
    lto=full
    werror=no
  )

  # Regular build
  scons "${_args[@]}"
}

package_godot() {
  cd $pkgbase-$pkgver-stable

  install -Dm755 bin/godot.linuxbsd.editor.$_CARCH.llvm "$pkgdir/usr/bin/godot"

  install -Dm644 icon.svg "$pkgdir/usr/share/pixmaps/$pkgname.svg"
  install -Dm644 misc/dist/linux/org.godotengine.Godot.desktop "$pkgdir/usr/share/applications/org.godotengine.Godot.desktop"
  install -Dm644 misc/dist/linux/org.godotengine.Godot.xml "$pkgdir/usr/share/mime/packages/org.godotengine.Godot.xml"

  install -Dm644 misc/dist/linux/godot.6 "$pkgdir/usr/share/man/man6/$pkgname.6"
  install -Dm644 LICENSE.txt "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}
