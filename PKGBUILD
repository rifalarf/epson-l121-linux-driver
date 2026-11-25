pkgname=epson-l121-driver-unlocked
pkgver=2.60.1.0.1
pkgrel=1
pkgdesc="Unlocked native Linux CUPS driver, ICC profile, and maintenance CLI (l121ctl) for Epson L121 / L120 Series"
arch=('x86_64')
url="https://download.ebz.epson.net/dsc/search/01/search/?OSC=LX"
license=('LGPL-2.1-or-later' 'custom:Epson End User Software License Agreement')
depends=('cups' 'glibc' 'gcc-libs' 'libcups' 'libjpeg-turbo' 'libusb' 'python')
makedepends=('autoconf' 'automake' 'libtool' 'gcc' 'make' 'curl' 'libarchive')
conflicts=('epson-inkjet-printer-201310w' 'epson-inkjet-printer-filter')
provides=('epson-inkjet-printer-201310w' 'epson-inkjet-printer-filter')

_rpm="epson-inkjet-printer-201310w-1.0.1-1.src.rpm"
source=("https://download3.ebz.epson.net/dsc/f/03/00/15/64/77/6f825c323a9f4b8429e3f9f0f4e9c50b1b15f583/${_rpm}")
sha256sums=('e7deaa3289f92a63077bb9a1fb291da5679acc9382180e879a6b6f74685e4660')
DLAGENTS=("https::/usr/bin/curl -A 'Mozilla/5.0' -fLC - --retry 3 --retry-delay 3 -o %o %u")

prepare() {
  cd "${srcdir}"
  bsdtar -xzf epson-inkjet-printer-201310w-1.0.1.tar.gz
  bsdtar -xzf epson-inkjet-printer-filter-1.0.2.tar.gz

  cd "${srcdir}/epson-inkjet-printer-filter-1.0.2"
  sed -i 's|CORE_LIBRARY_PATH=${prefix}/lib"$LSB_ARCH"|CORE_LIBRARY_PATH=/usr/lib|' configure.ac
  sed -i 's|CORE_RESOURCE_PATH=${prefix}/resource|CORE_RESOURCE_PATH=/usr/share/epson-inkjet-printer-filter/resource|' configure.ac
  sed -i 's|CFLAGS=-O2|CFLAGS="${CFLAGS:-"-O2"}"|' configure.ac
  sed -i 's|CXXFLAGS=-O2|CXXFLAGS="${CXXFLAGS:-"-O2"}"|' configure.ac
}

build() {
  cd "${srcdir}/epson-inkjet-printer-filter-1.0.2"
  autoreconf -vfi
  export LDFLAGS="${LDFLAGS//-Wl,--as-needed/} -Wl,--no-as-needed"
  export CFLAGS="${CFLAGS} -O2 -Wno-implicit-function-declaration -Wno-incompatible-pointer-types"
  ./configure --prefix=/usr
  make
}

package() {
  local drv="${srcdir}/epson-inkjet-printer-201310w-1.0.1"
  local flt="${srcdir}/epson-inkjet-printer-filter-1.0.2"

  # 1. Native CUPS filter binary
  install -Dm755 "${flt}/src/epson_inkjet_printer_filter" \
    "${pkgdir}/usr/lib/cups/filter/epson_inkjet_printer_filter"

  # 2. Core Epson shared libraries
  install -Dm755 "${drv}/lib64/libEpson_201310w.so.1.0.1" \
    "${pkgdir}/usr/lib/libEpson_201310w.so.1.0.1"
  ln -s libEpson_201310w.so.1.0.1 "${pkgdir}/usr/lib/libEpson_201310w.so.1"
  ln -s libEpson_201310w.so.1.0.1 "${pkgdir}/usr/lib/libEpson_201310w.so"

  install -Dm755 "${drv}/lib64/libEpson_201310w.MT.so.1.0.1" \
    "${pkgdir}/usr/lib/libEpson_201310w.MT.so.1.0.1"
  ln -s libEpson_201310w.MT.so.1.0.1 "${pkgdir}/usr/lib/libEpson_201310w.MT.so.1"
  ln -s libEpson_201310w.MT.so.1.0.1 "${pkgdir}/usr/lib/libEpson_201310w.MT.so"

  # 3. Resource data, watermarks, and maintenance payloads
  install -d "${pkgdir}/usr/share/epson-inkjet-printer-filter/resource"
  install -m644 "${drv}/resource/"* \
    "${pkgdir}/usr/share/epson-inkjet-printer-filter/resource/"

  install -d "${pkgdir}/usr/share/epson-inkjet-printer-filter/watermark"
  install -m644 "${drv}/watermark/"*.EID \
    "${pkgdir}/usr/share/epson-inkjet-printer-filter/watermark/"

  install -d "${pkgdir}/usr/share/epson-inkjet-printer-filter/maintenance"
  install -m644 "${startdir}/assets/maintenance/"*.bin \
    "${pkgdir}/usr/share/epson-inkjet-printer-filter/maintenance/"

  # 4. Windows L121 Official ICC Color Profile
  install -Dm644 "${startdir}/assets/Epson IJ Printer 07.icc" \
    "${pkgdir}/usr/share/cups/profiles/Epson IJ Printer 07.icc"
  install -Dm644 "${startdir}/assets/Epson IJ Printer 07.icc" \
    "${pkgdir}/usr/share/color/icc/epson/Epson IJ Printer 07.icc"

  # 5. Unlocked PPD for L121 / L120 Series
  install -Dm644 "${startdir}/EPSON_L121_Unlocked.ppd" \
    "${pkgdir}/usr/share/cups/model/epson-l121/EPSON_L121_Unlocked.ppd"

  # 6. Maintenance & Telemetry CLI + udev rules
  install -Dm755 "${startdir}/l121ctl" \
    "${pkgdir}/usr/bin/l121ctl"
  install -Dm644 "${startdir}/99-epson-l121.rules" \
    "${pkgdir}/usr/lib/udev/rules.d/99-epson-l121.rules"

  # 7. Licenses
  install -Dm644 "${drv}/COPYING.EPSON" \
    "${pkgdir}/usr/share/licenses/${pkgname}/COPYING.EPSON"
  install -Dm644 "${flt}/COPYING.LIB" \
    "${pkgdir}/usr/share/licenses/${pkgname}/COPYING.LIB"
}
