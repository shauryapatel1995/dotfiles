# $Id$
# Maintainer: Gaetan Bisson <bisson@archlinux.org>
# Contributor: Aaron Griffin <aaron@archlinux.org>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=openssh
pkgver=7.6p1
pkgrel=1
pkgdesc='Free version of the SSH connectivity tools'
url='https://www.openssh.com/portable.html'
license=('custom:BSD')
arch=('i686' 'x86_64')
makedepends=('linux-headers')
depends=('krb5' 'openssl' 'libedit' 'ldns')
optdepends=('xorg-xauth: X11 forwarding'
            'x11-ssh-askpass: input passphrase in X')
validpgpkeys=('59C2118ED206D927E667EBE3D3E5F56B6D920D30')
source=("https://ftp.openbsd.org/pub/OpenBSD/OpenSSH/portable/${pkgname}-${pkgver}.tar.gz"{,.asc}
        'openssl-1.1.0.patch'
        'sshdgenkeys.service'
        'sshd@.service'
        'sshd.service'
        'sshd.socket'
        'sshd.conf'
        'sshd.pam')
sha256sums=('a323caeeddfe145baaa0db16e98d784b1fbc7dd436a6bf1f479dfd5cd1d21723'
            'SKIP'
            '1fcae9fc461026d96d08b38457e7ffe281b4319caaada1508f9eb74c2566ba5d'
            '4031577db6416fcbaacf8a26a024ecd3939e5c10fe6a86ee3f0eea5093d533b7'
            '3a0845737207f4eda221c9c9fb64e766ade9684562d8ba4f705f7ae6826886e5'
            'c5ed9fa629f8f8dbf3bae4edbad4441c36df535088553fe82695c52d7bde30aa'
            'de14363e9d4ed92848e524036d9e6b57b2d35cc77d377b7247c38111d2a3defd'
            '4effac1186cc62617f44385415103021f72f674f8b8e26447fc1139c670090f6'
            '64576021515c0a98b0aaf0a0ae02e0f5ebe8ee525b1e647ab68f369f81ecd846')

backup=('etc/ssh/ssh_config' 'etc/ssh/sshd_config' 'etc/pam.d/sshd')

prepare() {
	cd $pkgname-$pkgver
	# OpenSSL 1.1.0 patch from http://vega.pgw.jp/~kabe/vsd/patch/openssh-7.4p1-openssl-1.1.0c.patch.html
	patch -Np1 -i ../openssl-1.1.0.patch
}

build() {
	cd "${srcdir}/${pkgname}-${pkgver}"

	./configure \
		--prefix=/usr \
		--sbindir=/usr/bin \
		--libexecdir=/usr/lib/ssh \
		--sysconfdir=/etc/ssh \
		--with-ldns \
		--with-libedit \
		--with-ssl-engine \
		--with-pam \
		--with-privsep-user=nobody \
		--with-kerberos5=/usr \
		--with-xauth=/usr/bin/xauth \
		--with-md5-passwords \
		--with-pid-dir=/run \

	make
}

check() {
	cd "${srcdir}/${pkgname}-${pkgver}"

	# Tests require openssh to be already installed system-wide,
	# also connectivity tests will fail under makechrootpkg since
        # it runs as nobody which has /bin/false as login shell.

	if [[ -e /usr/bin/scp && ! -e /.arch-chroot ]]; then
		make tests
	fi
}

package() {
	cd "${srcdir}/${pkgname}-${pkgver}"

	make DESTDIR="${pkgdir}" install

	ln -sf ssh.1.gz "${pkgdir}"/usr/share/man/man1/slogin.1.gz
	install -Dm644 LICENCE "${pkgdir}/usr/share/licenses/${pkgname}/LICENCE"

	install -Dm644 ../sshdgenkeys.service "${pkgdir}"/usr/lib/systemd/system/sshdgenkeys.service
	install -Dm644 ../sshd@.service "${pkgdir}"/usr/lib/systemd/system/sshd@.service
	install -Dm644 ../sshd.service "${pkgdir}"/usr/lib/systemd/system/sshd.service
	install -Dm644 ../sshd.socket "${pkgdir}"/usr/lib/systemd/system/sshd.socket
	install -Dm644 ../sshd.conf "${pkgdir}"/usr/lib/tmpfiles.d/sshd.conf
	install -Dm644 ../sshd.pam "${pkgdir}"/etc/pam.d/sshd

	install -Dm755 contrib/findssl.sh "${pkgdir}"/usr/bin/findssl.sh
	install -Dm755 contrib/ssh-copy-id "${pkgdir}"/usr/bin/ssh-copy-id
	install -Dm644 contrib/ssh-copy-id.1 "${pkgdir}"/usr/share/man/man1/ssh-copy-id.1

	sed \
		-e '/^#ChallengeResponseAuthentication yes$/c ChallengeResponseAuthentication no' \
		-e '/^#PrintMotd yes$/c PrintMotd no # pam does that' \
		-e '/^#UsePAM no$/c UsePAM yes' \
		-i "${pkgdir}"/etc/ssh/sshd_config
}
