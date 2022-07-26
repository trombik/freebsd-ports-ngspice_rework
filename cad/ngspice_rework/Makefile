# Created by: AMAKAWA Shuhei <amakawa@jp.FreeBSD.org>

PORTNAME=	ngspice_rework
PORTVERSION=	36
PORTREVISION=	1
CATEGORIES=	cad
MASTER_SITES=	SF/ngspice/ng-spice-rework/${PORTVERSION}
DISTNAME=	ngspice-${PORTVERSION}

MAINTAINER=	kevinz5000@gmail.com
COMMENT=	Mixed-signal circuit simulator derived from Spice and Cider

LICENSE=	BSD3CLAUSE BSD4CLAUSE LGPL21+
LICENSE_COMB=	multi
LICENSE_FILE=	${WRKSRC}/COPYING

BROKEN_aarch64=	fails to link: missing sbrk

LIB_DEPENDS=	libfftw3.so:math/fftw3

USES=		compiler:c11 gmake libtool ncurses readline autoreconf pkgconfig xorg

GNU_CONFIGURE=	yes

OPTIONS_DEFINE=	DEBUG

USE_XORG=	ice sm x11 xaw xext xmu xt xft
USE_LDCONFIG=		yes
SHLIB_CONFIGURE_ARGS=	--with-ngshared
CONFIGURE_ARGS+=	--enable-cider --enable-xspice --with-readline=yes --enable-shared --with-x
DEBUG_CONFIGURE_ENABLE=	debug

INSTALL_TARGET=	install-strip

post-build:
	# as the configure does not allow to build shlib and the binary at the
	# same time, run configure again after building the binary.
	cd ${WRKSRC} && \
		${SETENV} CC="${CC}" CPP="${CPP}" CXX="${CXX}" \
	    CFLAGS="${CFLAGS}" CPPFLAGS="${CPPFLAGS}" CXXFLAGS="${CXXFLAGS}" \
	    LDFLAGS="${LDFLAGS}" LIBS="${LIBS}" \
	    INSTALL="/usr/bin/install -c" \
	    INSTALL_DATA="${INSTALL_DATA}" \
	    INSTALL_LIB="${INSTALL_LIB}" \
	    INSTALL_PROGRAM="${INSTALL_PROGRAM}" \
	    INSTALL_SCRIPT="${INSTALL_SCRIPT}" \
	    ${CONFIGURE_ENV} ${CONFIGURE_CMD} `echo ${CONFIGURE_ARGS} | sed -e 's/--with-x//'` ${SHLIB_CONFIGURE_ARGS}

	cd ${WRKSRC} && \
		${SETENV} ${MAKE_ENV} ${MAKE_CMD} ${MAKE_FLAGS} ${MAKEFILE} ${_MAKE_JOBS} ${MAKE_ARGS:N${DESTDIRNAME}=*}

post-install:
	# install the binary as do-install installs shlib, but not the binary
	${INSTALL_PROGRAM} ${WRKSRC}/src/ngspice ${STAGEDIR}${PREFIX}/bin/ngspice
	${INSTALL_MAN} ${WRKSRC}/man/man1/ngspice.1 ${STAGEDIR}${MAN1PREFIX}/man/man1/ngspice.1
	@cd ${STAGEDIR}${PREFIX} && \
		${RM} bin/cmpp man/man1/cmpp.1

.include <bsd.port.mk>
