pkgname=zsh
pkgver=5.9
pkgrel=5
pkgdesc='A very advanced and programmable command interpreter (shell) for UNIX'
arch=('x86_64')
url='http://www.zsh.org/'
license=('custom')
depends=('pcre' 'libcap' 'gdbm')
makedepends=('pcre' 'libcap' 'gdbm' 'clang')
backup=('etc/zsh/zprofile')
install=zsh.install
source=("http://www.zsh.org/pub/${pkgname}-${pkgver}.tar.xz"
        "http://www.zsh.org/pub/${pkgname}-${pkgver}-doc.tar.xz"
        '0001-50629-do-not-use-egrep-in-tests.patch'
        'fix-autocompletion.patch'
        'zprofile')
sha256sums=('9b8d1ecedd5b5e81fbf1918e876752a7dd948e05c1a0dba10ab863842d45acd5'
            '6f7c091249575e68c177c5e8d5c3e9705660d0d3ca1647aea365fd00a0bd3e8a'
            'bcce55ff5a81a321c501b7749a7d3e2031004c0ab999d80c35eeb4b9ce7efdf0'
            'e7060f64936bcc94aeaf44e69b6d476917a8c14cd339a3bc779c080fb01bb2f7'
            'b77f3879b0e88990aa70a01d974f7cbf06f870f8de5093fa4b7b264bc54575c0')

prepare() {
	cd "${srcdir}/${pkgname}-${pkgver}"

	# 50629: do not use egrep in tests
	patch -Np1 < ../0001-50629-do-not-use-egrep-in-tests.patch

	# https://github.com/zsh-users/zsh/commit/3e3cfabcc74dc79d4d8717c4e5859d8d01be6c54
	# https://www.zsh.org/mla/workers/2022/msg00652.html
	patch -Np1 < ../fix-autocompletion.patch

	# Set correct keymap path
	sed -i 's#/usr/share/keymaps#/usr/share/kbd/keymaps#g' Completion/Unix/Command/_loadkeys

	# Fix usb.ids path
	sed -i 's#/usr/share/misc/usb.ids#/usr/share/hwdata/usb.ids#g' Completion/Linux/Command/_lsusb

	# Remove unneeded and conflicting completion scripts
	for _fpath in AIX BSD Cygwin Darwin Debian Mandriva openSUSE Redhat Solaris; do
		rm -rf Completion/$_fpath
		sed "s#\s*Completion/$_fpath/\*/\*##g" -i Src/Zle/complete.mdd
	done
	rm -f Completion/Linux/Command/{_pkgtool,rpmbuild,yast}

	# force generation of documentation with correct paths
	rm -f Doc/version.yo
}

build() {
	cd "${srcdir}/${pkgname}-${pkgver}"

	CC=clang ./configure --prefix=/usr \
		--bindir=/bin \
		--docdir=/usr/share/doc/zsh \
		--htmldir=/usr/share/doc/zsh/html \
		--enable-etcdir=/etc/zsh \
		--enable-zshenv=/etc/zsh/zshenv \
		--enable-zlogin=/etc/zsh/zlogin \
		--enable-zlogout=/etc/zsh/zlogout \
		--enable-zprofile=/etc/zsh/zprofile \
		--enable-zshrc=/etc/zsh/zshrc \
		--enable-maildir-support \
		--with-term-lib='ncursesw' \
		--enable-multibyte \
		--enable-function-subdirs \
		--enable-fndir=/usr/share/zsh/functions \
		--enable-scriptdir=/usr/share/zsh/scripts \
		--with-tcsetpgrp \
		--enable-pcre \
		--enable-cap \
		--enable-zsh-secure-free
	make all
}
#
# check() {
#	cd "${srcdir}/${pkgbase}-${pkgver}"
#	HOME="${srcdir}" make check
# }

package() {
	cd "${srcdir}/${pkgname}-${pkgver}"
	make DESTDIR="${pkgdir}/" install all
	make DESTDIR="${pkgdir}/" install.info
	install -D -m644 "${srcdir}/zprofile" "${pkgdir}/etc/zsh/zprofile"
	install -D -m644 Doc/zsh.pdf "${pkgdir}/usr/share/doc/zsh/zsh.pdf"
	install -D -m644 LICENCE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}
