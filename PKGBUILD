# Maintainer: steadfasterX <steadfasterX@gmail.com>
pkgname=cav-linux-dkms
_pkgname=${pkgname%-*}
pkgver=1.1.268025
pkgrel=1
arch=('x86_64')
pkgdesc='COMODO Antivirus and Mail Gateway for Linux (DKMS for redirfs module)'
url='https://www.comodo.com/home/internet-security/antivirus-for-linux.php'
license=('custom')
#noextract=('comodo-${_pkgname}_x64.deb')
depends=('sudo' 'cscope' 'dkms')
conflicts=( 'eea'
            'esets'
            'eea-dkms'
            'eea7-dkms')
install=${_pkgname}.install
# https://www.comodo.com/home/internet-security/antivirus-for-linux.php
source=( "https://cdn.download.comodo.com/cis/download/installs/linux/${_pkgname}_x64.deb"
         "git+https://github.com/redplait/redirfs.git"
         "${_pkgname}.install"
         "dkms.conf"
         "redirfs.patch"
         "cmdagent.patch"
         "cmgdaemon.patch")
sha256sums=('325b819b041a7b27026ba85f66ea808d0d11ad39d94bc13ae6d95802413495b6'
            'SKIP'
            'eb2a9c65a3e464e14552f3694e78eadd1c8cff0fe3c10f75dbef7579dcc8f295'
            '6121391a86f36b28f5b228d0ae9d3ee2d8a0a46795066f5cd498f1c34321ff1a'
            '84c7953070852d3fc2b4a2f1e9af4ef1e66b2f7d8bf9e5f72fcbdcc4e00ac23c'
            '9b4ed0c2b11798ac15366a52d8440305b28597c008c9286c6bc114e65df976da'
            '4d963c47996e1c96c597c7769931183ccc1e051720b6fc3aa1d8a9ca73c15ba8')

prepare(){
    # fixes:
    # - "scripts/Makefile.build:44: arch/x86/entry/syscalls/Makefile: no such file ..." and others
    # - wrong modules install path
    patch -p1 < redirfs.patch
}

check(){
    # module signing requires proper keys to be installed first
    for m in /lib/modules/$(uname -r)/build/certs/signing_key.pem /lib/modules/$(uname -r)/build/certs/signing_key.x509;do
        [ ! -f $m ] && echo -e "\n******** ERROR ********\nsigning_key for kernel modules missing ($m)! ABORTED.\nCheck: https://superuser.com/a/1659287 for how to create these.\n" && return 3
    done
    return 0
}

package() {
    # prepare dirs
    mkdir -p ${pkgdir}/usr/src
    mkdir -p ${pkgdir}/etc/init.d

    # uncompress base packages
    bsdtar -xf ${_pkgname}_x64.deb
    bsdtar -xf data.tar.gz -C ${pkgdir}/
    [ ! -d "${pkgdir}/opt/COMODO/pkgscripts" ] && mkdir ${pkgdir}/opt/COMODO/pkgscripts
    bsdtar -xf control.tar.gz -C ${pkgdir}/opt/COMODO/pkgscripts

    # update redirfs & avsflt drivers to allow compiling on kernel >= v5
    cd ${srcdir}/redirfs/
    tar xf ${pkgdir}/opt/COMODO/driver.tar
    cp -a src/* driver/
    tar cf ${pkgdir}/opt/COMODO/driver.tar driver
    cd $startdir

    # DKMS config
    install -Dm644 dkms.conf "${pkgdir}"/usr/src/${_pkgname}-${pkgver}/dkms.conf
    local MODNAME1=redirfs
    local MODNAME2=avflt
    sed -e "s/@_PKGBASE@/$_pkgname/" \
        -e "s/@PKGVER@/${pkgver}/" \
        -e "s/@MODNAME1@/$MODNAME1/" \
        -e "s/@MODNAME2@/$MODNAME2/" \
        -i "${pkgdir}"/usr/src/${_pkgname}-${pkgver}/dkms.conf
    cp -a ${srcdir}/redirfs/src/* ${pkgdir}/usr/src/${_pkgname}-${pkgver}/

    # FIXME: convert to systemd..? problem: the CAV gui expects the init.d paths....
    # making the init scripts Manjaro/Arch compatible
    # this works even on systemd systems and seems to be required by the CAV gui (likely hard coded paths to init.d there)
    echo "path: $(pwd)"
    patch -p1 < cmdagent.patch
    patch -p1 < cmgdaemon.patch
    install -Dm755 ${pkgdir}/opt/COMODO/load_cmdagent.sh ${pkgdir}/etc/init.d/cmdavd
    install -Dm755 ${pkgdir}/opt/COMODO/load_cmgdaemon.sh ${pkgdir}/etc/init.d/cmdmgd

    # finally install cav
    install -d $pkgdir/opt
    install -d $pkgdir/usr
    install -Dm644 $pkgdir/opt/COMODO/doc/eula_free.txt "$pkgdir/usr/share/licenses/$_pkgname/LICENSE"
}
