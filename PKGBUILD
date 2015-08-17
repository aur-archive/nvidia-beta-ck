# Maintainer: Zbynek Novotny <znovotny@gmail.com>
# Contributor: graysky <graysky AT archlinux dot us>
# Contributor: Dan Vratil <vratil@progdansoft.com>

pkgname="nvidia-beta-ck"
pkgver=349.16
pkgrel=2
pkgdesc="NVIDIA beta drivers for linux-ck."
arch=("i686" "x86_64")
kernver="4.0"
extramods="extramodules-${kernver}-ck"
exactkernver="$(cat /usr/lib/modules/${extramods}/version)"
provides=("nvidia-beta-ck=${pkgver}")
depends=("linux-ck>=${kernver}" "nvidia-utils-beta=${pkgver}")
makedepends=("linux-ck-headers>=${kernver}")
conflicts=("nvidia-ck" "nvidia-ck-bumblebee" "nvidia-ck-pax-bumblebee")
license=("custom")
url="http://www.nvidia.com/"
install=nvidia.install
[ "${CARCH}" = "i686" ] && ARCH=x86    && srcname=NVIDIA-Linux-x86-${pkgver}                && md5sums=("59597d5dfaaf0fc83a5e78d29a97ccac")
[ "${CARCH}" = x86_64 ] && ARCH=x86_64 && srcname=NVIDIA-Linux-x86_64-${pkgver}-no-compat32 && md5sums=("3e04208affde8c35a8b96975eec0486b")
source=("ftp://download.nvidia.com/XFree86/Linux-$ARCH/${pkgver}/${srcname}.run")

build()
{
	# Extract the nvidia drivers
	cd "${srcdir}"
	if [ -d ${srcname} ]; then
		rm -rf ${srcname}
	fi
	sh ${srcname}.run --extract-only
	cd ${srcname}/kernel
	make SYSSRC=/usr/lib/modules/${exactkernver}/build module
}

package() 
{
	cd "${srcdir}/${srcname}/kernel"

    module_path="${pkgdir}/usr/lib/modules/${extramods}/kernel/drivers/video"

	# Install kernel module
	mkdir -p "${module_path}"
	install -m644 nvidia.ko "${module_path}"

	# Blacklist nouveau since 2.6.34
    # We have to store the blacklist directive in a unique conffile
    # whose name does not collide with other packages (e.g. nvidia-beta)
	mkdir -p "${pkgdir}/etc/modprobe.d/"
	echo "blacklist nouveau" >> "${pkgdir}/etc/modprobe.d/nouveau_blacklist-ck.conf"
    gzip "${module_path}/nvidia.ko"
}

