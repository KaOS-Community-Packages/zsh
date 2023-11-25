pkgname=zsh
pkgver=5.9
pkgrel=4
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
sha512sums=('SKIP' 'SKIP' 'SKIP' 'SKIP' 'SKIP')

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
	rm -f Completion/Linux/Command/_pkgtool

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
