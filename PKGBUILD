pkgname=zsh
pkgver=5.9
pkgrel=3
pkgdesc='A very advanced and programmable command interpreter (shell) for UNIX'
depends=('pcre' 'libcap' 'gdbm')
backup=('etc/zsh/zprofile')
install=zsh.install
arch=('x86_64')
url='http://www.zsh.org/'
license=('custom')
makedepends=('pcre' 'libcap' 'gdbm' 'clang')
source=("http://www.zsh.org/pub/${pkgname}-${pkgver}.tar.xz"
        "http://www.zsh.org/pub/${pkgname}-${pkgver}-doc.tar.xz"
        '0001-50629-do-not-use-egrep-in-tests.patch'
				'fix-autocompletion.patch'
        'zprofile')
md5sums=('182e37ca3fe3fa6a44f69ad462c5c30e'
         '8db53446f613521fb4f9b0bd9f6adda1'
         '9ed41f76a785022be76f8b6b8f46e086'
         '102fab159efa5a973e8def6f126f753f'
         '92946911e57d05f1a3f1272b136c85b5')

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
	rm -f  Completion/Linux/Command/_{pkgtool,rpmbuild,yast}
	rm -f  Completion/Unix/Command/_{osc,systemd}
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
	make
}

#check() {
#	cd "${srcdir}/${pkgname}-${pkgver}"
#	HOME="${srcdir}" make check
#}

package() {
	cd "${srcdir}/${pkgname}-${pkgver}"
	make DESTDIR="${pkgdir}/" install
	make DESTDIR="${pkgdir}/" install.info install.html
	install -D -m644 "${srcdir}/zprofile" "${pkgdir}/etc/zsh/zprofile"
	install -D -m644 LICENCE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}
