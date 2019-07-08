pkgname=brave-vaapi
pkgver=0.66.99
pkgrel=1
pkgdesc='A web browser that stops ads and trackers by default. With VA-API enabled.'
arch=('x86_64')
url='https://www.brave.com/download'
license=('custom')
depends=('gtk3' 'nss' 'alsa-lib' 'gconf' 'libxss' 'ttf-font' 'libva')
makedepends=('git' 'npm' 'python2' 'icu' 'glibc' 'gperf' 'java-runtime-headless')
optdepends=('cups: Printer support'
            'pepper-flash: Adobe Flash support'
            'libgnome-keyring: Enable GNOME keyring support')
provides=('brave')
conflicts=('brave')
source=("git+https://github.com/brave/brave-browser.git#tag=v${pkgver}"
        'chromium-vaapi-fix.patch'
        'brave-vaapi-enable.patch'
        'brave-build-fix.patch'
        'brave-launcher'
        'brave.desktop'
        'logo.png')
sha256sums=('SKIP'
            '83cca45acc2515b9c83f34844494a8788f85bb04c36834b88e905c490988d9bb'
            '2b07eabd8b3d42456d2de44f6dca6cf2e98fa06fc9b91ac27966fca8295c5814'
            '45c3ab1e7efc4508f87403446ddd8017c695ce669c8467353e255f2a8fb933c6'
            'd3c9be4babc754271ad8d6169705ec319fa45d406184d7677fa83a4a2df49acf'
            '4c3dbceaf7d791f94d6e46c26c62a1b500bdf3a25a2ecf97a5075e86649b63ef'
            '4a585cb8740f4c9ba267f0df19d894eb9fae1b9a6af4a3e44737b7d0bcbc104a')

prepare() {
    # Apply Brave patches
    cd brave-browser
    patch -Np1 -i "${srcdir}/brave-vaapi-enable.patch"
    #patch -Np1 -i "${srcdir}/brave-build-fix.patch"

    # Hack to prioritize python2 in PATH
    mkdir -p "${HOME}/bin"
    ln -s /usr/bin/python2 "${HOME}/bin/python"
    ln -s /usr/bin/python2-config "${HOME}/bin/python-config"
    export PATH="${HOME}/bin:${PATH}"

    # Prepare the environment
    npm install
    npm run init

    # Apply Chromium patches
    cd src/
    patch -Np1 -i "${srcdir}/chromium-vaapi-fix.patch"
    
    #Hack it
    sed -e 's/enable_distro_version_check = true/enable_distro_version_check = false/g' -i chrome/installer/linux/BUILD.gn
}

build() {
    cd brave-browser
    # npm run build Release
    npm run create_dist Release --debug_build=false --official_build=true
}

package() {
    install -d -m0755 "${pkgdir}/usr/lib"
    cp -a --reflink=auto brave-browser/src/out/Release "${pkgdir}/usr/lib/brave-vaapi"

    install -Dm0755 brave-launcher "${pkgdir}/usr/bin/brave"
    install -Dm0644 -t "${pkgdir}/usr/share/applications/" brave.desktop
    install -Dm0644 logo.png "${pkgdir}/usr/share/pixmaps/brave.png"
    install -Dm0644 -t "${pkgdir}/usr/share/licenses/${pkgname}" brave-browser/LICENSE
    install -Dm0644 -t "${pkgdir}/usr/share/licenses/${pkgname}" brave-browser/src/brave/components/brave_sync/extension/brave-sync/node_modules/electron/dist/LICENSES.chromium.html
}

# vim:set ts=4 sw=4 et:
