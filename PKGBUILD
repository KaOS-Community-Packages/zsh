pkgname=zsh
pkgver=5.9
pkgrel=1
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
        'zprofile')
md5sums=('182e37ca3fe3fa6a44f69ad462c5c30e'
         '8db53446f613521fb4f9b0bd9f6adda1'
         '92946911e57d05f1a3f1272b136c85b5')

prepare() {
	cd "${srcdir}/${pkgname}-${pkgver}"

	# Set correct keymap path
	sed -i 's#/usr/share/keymaps#/usr/share/kbd/keymaps#g' Completion/Unix/Command/_loadkeys

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

check() {
	cd "${srcdir}/${pkgname}-${pkgver}"
	HOME="${srcdir}" make check
}

package() {
	cd "${srcdir}/${pkgname}-${pkgver}"
	make DESTDIR="${pkgdir}/" install
	make DESTDIR="${pkgdir}/" install.info install.html
	install -D -m644 "${srcdir}/zprofile" "${pkgdir}/etc/zsh/zprofile"
	install -D -m644 LICENCE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}
