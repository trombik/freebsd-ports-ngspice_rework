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
DEFAULT_CONFIGURE_ARGS=	--enable-cider --enable-xspice --with-readline=yes --enable-shared

OPTIONS_DEFINE=	DEBUG

USE_XORG=	ice sm x11 xaw xext xmu xt xft
USE_LDCONFIG=		yes
SHLIB_CONFIGURE_ARGS=	--with-ngshared
X11_CONFIGURE_ARGS=	--with-x
CONFIGURE_ARGS+=	${DEFAULT_CONFIGURE_ARGS} ${SHLIB_CONFIGURE_ARGS}
DEBUG_CONFIGURE_ENABLE=	debug

INSTALL_TARGET=	install-strip

post-build:
	# as the configure does not allow to build shlib and the binary at the
	# same time, run configure again after building the shlib.
	# XXX shlib must be built before the binary due to an interesting build
	# system.
	cd ${WRKSRC} && \
		${SETENV} CC="${CC}" CPP="${CPP}" CXX="${CXX}" \
	    CFLAGS="${CFLAGS}" CPPFLAGS="${CPPFLAGS}" CXXFLAGS="${CXXFLAGS}" \
	    LDFLAGS="${LDFLAGS}" LIBS="${LIBS}" \
	    INSTALL="/usr/bin/install -c" \
	    INSTALL_DATA="${INSTALL_DATA}" \
	    INSTALL_LIB="${INSTALL_LIB}" \
	    INSTALL_PROGRAM="${INSTALL_PROGRAM}" \
	    INSTALL_SCRIPT="${INSTALL_SCRIPT}" \
	    ${CONFIGURE_ENV} ${CONFIGURE_CMD} `${ECHO} ${CONFIGURE_ARGS} | ${SED} -e 's/--with-ngshared//'` ${X11_CONFIGURE_ARGS}

	cd ${WRKSRC} && \
		${SETENV} ${MAKE_ENV} ${MAKE_CMD} ${MAKE_FLAGS} ${MAKEFILE} ${_MAKE_JOBS} ${MAKE_ARGS:N${DESTDIRNAME}=*}

post-install:
	# install the shlib as do-install installs the binary, but not the shlib
	${INSTALL} -d ${STAGEDIR}${PREFIX}/include/ngspice
	${INSTALL_LIB} ${WRKSRC}/src/.libs/libngspice.so ${STAGEDIR}${PREFIX}/lib/
	${INSTALL_LIB} ${WRKSRC}/src/.libs/libngspice.so.0 ${STAGEDIR}${PREFIX}/lib/
	${INSTALL_LIB} ${WRKSRC}/src/.libs/libngspice.so.0.0.2 ${STAGEDIR}${PREFIX}/lib/
	${INSTALL_DATA} ${WRKSRC}/src/include/ngspice/sharedspice.h ${STAGEDIR}${PREFIX}/include/ngspice/
	${INSTALL_DATA} ${WRKSRC}/ngspice.pc ${STAGEDIR}${PREFIX}/libdata/pkgconfig/

	@cd ${STAGEDIR}${PREFIX} && \
		${RM} bin/cmpp man/man1/cmpp.1

.include <bsd.port.mk>
