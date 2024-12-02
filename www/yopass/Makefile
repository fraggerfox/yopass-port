PORTNAME=	yopass
PORTVERSION=	11.17.0
CATEGORIES=	www textproc
MASTER_SITES=	LOCAL/fox/:js
DISTFILES=	yopass-${DISTVERSION}-node_modules.tgz:js

MAINTAINER=	fox@FreeBSD.org
COMMENT=	Share secrets securely with ease
WWW=		https://yopass.se/

LICENSE=	APACHE20

BROKEN_aarch64=	fails to build
BROKEN_i386=	fails to build

BUILD_DEPENDS=	yarn${NODEJS_SUFFIX}>=0:www/yarn${NODEJS_SUFFIX}
RUN_DEPENDS=	${LOCALBASE}/bin/memcached:databases/libmemcached

USES=		go:modules nodejs:20,build
USE_RC_SUBR=	yopass

USE_GITHUB=	yes
GH_ACCOUNT=	jhaals
GH_PROJECT=	yopass

GO_MODULE=	github.com/jhaals/yopass
GO_MOD_DIST=	github
GO_TARGET=	./cmd/yopass ./cmd/yopass-server

post-extract:
	@${RLN} ${WRKDIR}/node_modules ${WRKSRC}/website/node_modules

pre-build:
	cd ${WRKSRC}/website && ${SETENV} ${MAKE_ENV} yarn build

do-install:
	${MKDIR} ${STAGEDIR}${WWWDIR}
	${INSTALL_PROGRAM} ${WRKDIR}/bin/yopass-server ${STAGEDIR}${WWWDIR}
	${INSTALL_PROGRAM} ${WRKDIR}/bin/yopass-server ${STAGEDIR}${PREFIX}/bin
	${RLN} ${STAGEDIR}${WWWDIR}/yopass-server ${STAGEDIR}${PREFIX}/bin/yopass-server

post-install:
	${MKDIR} ${STAGEDIR}${WWWDIR}/public
	cd ${WRKSRC}/website/build && ${COPYTREE_SHARE} . ${STAGEDIR}${WWWDIR}/public/
	${MKDIR} ${STAGEDIR}/var/run/${PORTNAME}

#
# To update the opengist-${DISTVERSION}-node_modules.tgz archive:
# 1. Comment out the DISTFILES and post-extract lines
# 2. Run 'make makesum regenerate-node_modules-distfile clean'
# 3. Upload the archive
# 4. Uncomment the lines from step #1
# 5. Run 'make makesum'
#

regenerate-node_modules-distfile: patch
	cd ${WRKSRC}/website && \
	${RM} -r node_modules && \
	${MAKE_ENV} yarn install --prefix ${WRKSRC}/website && \
	${TAR} czf ${DISTDIR}/yopass-${DISTVERSION}-node_modules.tgz node_modules && \
	${ECHO} "Please upload the file ${DISTDIR}/yopass-${DISTVERSION}-node_modules.tgz"

.include <bsd.port.mk>
