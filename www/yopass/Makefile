PORTNAME=	yopass
PORTVERSION=	12.5.0
CATEGORIES=	wip www textproc
MASTER_SITES=	https://raw.githubusercontent.com/jhaals/yopass/refs/tags/${PORTVERSION}/:go \
		https://codeload.github.com/${GH_ACCOUNT}/${GH_PROJECT}/tar.gz/${GH_TAG}?dummy=/ \
		LOCAL/fox/:js
DISTFILES=	${GH_ACCOUNT}-${GH_PROJECT}-${GH_TAG}_GH0.tar.gz \
		${GH_PROJECT}-${GH_TAG}-node_modules.tgz:js \
		go.mod:go

MAINTAINER=	fox@FreeBSD.org
COMMENT=	Share secrets securely with ease
WWW=		https://yopass.se/

#CONFLICTS_INSTALL=	yopass
LICENSE=	APACHE20

ONLY_FOR_ARCHS=	amd64

BUILD_DEPENDS=	yarn${NODEJS_SUFFIX}>=0:www/yarn${NODEJS_SUFFIX}
RUN_DEPENDS=	${LOCALBASE}/bin/memcached:databases/memcached

USES=		go:1.25,modules nodejs:20,build
USE_RC_SUBR=	yopass

USE_GITHUB=	nodefault
GH_ACCOUNT=	jhaals
GH_PROJECT=	yopass
GH_TAG=		${PORTVERSION}

GO_MODULE=	github.com/jhaals/yopass
GO_TARGET=	./cmd/yopass ./cmd/yopass-server
GO_ENV+=	GOTOOLCHAIN=local+path
GO_MOD_DIST=	github

post-extract:
	@${RLN} ${WRKDIR}/node_modules ${WRKSRC}/website/node_modules

pre-build:
	cd ${WRKSRC}/website && ${SETENV} ${MAKE_ENV} yarn build

do-install:
	${MKDIR} ${STAGEDIR}${WWWDIR}
	${INSTALL_PROGRAM} ${WRKDIR}/bin/yopass-server ${STAGEDIR}${WWWDIR}
	${INSTALL_PROGRAM} ${WRKDIR}/bin/yopass ${STAGEDIR}${PREFIX}/bin
	${RLN} ${STAGEDIR}${WWWDIR}/yopass-server ${STAGEDIR}${PREFIX}/bin/yopass-server

post-install:
	${MKDIR} ${STAGEDIR}${WWWDIR}/public
	cd ${WRKSRC}/website/dist && ${COPYTREE_SHARE} . ${STAGEDIR}${WWWDIR}/public/
	${MKDIR} ${STAGEDIR}/var/run/${PORTNAME}

#
# To update the opengist-${GH_TAG}-node_modules.tgz archive:
# 1. Comment out the *-node_modules.tar.gz in DISTFILES and post-extract lines
# 2. Run 'make makesum regenerate-node_modules-distfile clean'
# 3. Upload the archive
# 4. Uncomment the lines from step #1
# 5. Run 'make makesum'
#

regenerate-node_modules-distfile: patch
	cd ${WRKSRC}/website && \
	${RM} -r node_modules && \
	${MAKE_ENV} yarn install && \
	${TAR} czf ${DISTDIR}/yopass-${GH_TAG}-node_modules.tgz node_modules && \
	${ECHO} "Please upload the file ${DISTDIR}/yopass-${GH_TAG}-node_modules.tgz"

.include <bsd.port.mk>
