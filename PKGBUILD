# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Contributor: Simon Legner <Simon.Legner@gmail.com>
# Contributor: Chris Molozian (novabyte) <chris DOT molozian AT gmail DOT com>
# Contributor: Sanjuro Makabe (itti) <vuck AT gmx DOT de>

_pkg=gradle
pkgver="8.8"
_pkgname="${_pkg}${pkgver}"
pkgbase="${_pkgname}"
pkgname=(
  "${_pkgname}"
  "${_pkgname}-doc"
  "${_pkgname}-src"
)
pkgrel=1
pkgdesc='Powerful build system for the JVM'
url="https://${_pkg}.org"
arch=(
  'any'
)
license=(
  'Apache'
)
depends=(
  'java-environment<=22'
  'bash'
  'which'
  'coreutils'
  'findutils'
  'sed'
)
makedepends=(
  'git'
  'asciidoc'
  'xmlto'
  'groovy'
  'java-environment=11'
)
_dl="https://services.gradle.org/distributions"
_tarname="${_pkg}-${pkgver}"
source=(
  "${_dl}/${_tarname}-src.zip"
  "${_dl}/${_tarname}-all.zip"
  "${_pkg}.sh"
)
sha256sums=(
  'b92a0f4e6206735cfa4c96515f75fe6eb87df4e120fad6533c28d17c23ffb8d9'
  'f8b4f4772d302c8ff580bc40d0f56e715de69b163546944f787c87abf209c961'
  '6f3472486278252417af49196847ba465b56819d286658fcdf918687f89ee032'
)
sha512sums=(
  '14f445c0f5f2e4944da7fddf4e65d98322c9a99a4f5b7347f7a200496fa2448961978d8e4c708469c6f4d46088eec4d20b64ecb0356e35be6d7f4577a7d07f1a'
  '09aaa2f5b16a3095f0ad7d2074ba65755c3ae7d3b57ccec9f52a31bb2192071addc27db15e86d33e8215b2d095279612f817019535aa923bd4fab43d1fc26f94'
  'a50b6cf8281b56b80f55a20ac9316e1eed6887da1d191ad575dec140c9819711644d7077c4dc693b8cb0f1b08ceba0033ba88b5ad138d33ffb73b786c0d4bf81'
)

prepare() {
  local \
    _files=()
  cd \
    "${_tarname}"
  _files=(
    "build-logic-commons/module-identity/build.${_pkg}.kts"
    "build-logic-commons/${_pkg}-plugin/build.${_pkg}.kts"
    "build-logic/jvm/src/main/kotlin/${_pkg}build.unittest-and-compile.${_pkg}.kts"
    "build-logic-commons/${_pkg}-plugin/src/main/kotlin/${_pkg}build/commons/JavaPluginExtensions.kt"
    "build-logic-commons/code-quality-rules/build.${_pkg}.kts"
    "build-logic-commons/basics/build.${_pkg}.kts"
    "platforms/jvm/language-java/src/integTest/groovy/org/${_pkg}/jvm/toolchain/JavaToolchainDownloadIntegrationTest.groovy"
  )
  # remove ADOPTIUM contraint from all build related files
  sed \
    -i \
    '/JvmVendorSpec.ADOPTIUM/d' \
    "${_files[@]}"
  # inhibit automatic download of binary gradle
  sed \
    -i \
    "s#distributionUrl=.*#distributionUrl=file\:${srcdir}/${_tarname}-all.zip#" \
    "${_pkg}/wrapper/${_pkg}-wrapper.properties"
}

build() {
  cd \
    "${_tarname}"
  # requires java language level 6, which >=13 has dropped
  export \
    PATH="/usr/lib/jvm/java-11-openjdk/bin:${PATH}"
  "./${_pkg}w" \
    installAll \
      -P"org.${_pkg}.java.installations.auto-download"="false" \
      -P"finalRelease"="true" \
      -P"${_pkg}_installPath"="$(pwd)/dist" \
      --no-configuration-cache
}

package_gradle() {
  cd \
    "${_tarname}/dist"
  optdepends=(
    "${_pkgname}-doc: gradle documentation"
    "${_pkgname}-src: gradle sources"
  )
  # install profile.d script
  install \
    -Dm755 \
    "${srcdir}/${_pkg}.sh" \
    "${pkgdir}/etc/profile.d/${_pkgname}.sh"
  # create the necessary directory structure
  install \
    -d \
    "${pkgdir}/usr/share/java/${_pkgname}/bin"
  install \
    -d \
    "${pkgdir}/usr/share/java/${_pkgname}/lib/"{"plugins","agents"}
  install \
    -d \
    "${pkgdir}/usr/share/java/${_pkgname}/init.d"
  install \
    -d \
    "${pkgdir}/usr/bin"
  # copy across jar files
  install \
    -Dm644 \
    "lib/"*".jar" \
    "${pkgdir}/usr/share/java/${_pkgname}/lib"
  install \
    -Dm644 \
    "lib/plugins/"*".jar" \
    "${pkgdir}/usr/share/java/${_pkgname}/lib/plugins"
  install \
    -Dm644 \
    "lib/agents/"*".jar" \
    "${pkgdir}/usr/share/java/${_pkgname}/lib/agents"
  # copy across supporting text documentation and scripts
  install \
    -Dm644 \
    "NOTICE" \
    "${pkgdir}/usr/share/java/${_pkgname}"
  install \
    -Dm755 \
    "bin/${_pkg}" \
    "${pkgdir}/usr/share/java/${_pkgname}/bin"
  install \
    -Dm644 \
    "init.d/"*"."* \
    "${pkgdir}/usr/share/java/${_pkgname}/init.d"
  # link gradle script to /usr/bin
  ln \
    -s \
    "/usr/share/java/${_pkgname}/bin/${_pkg}" \
    "${pkgdir}/usr/bin/${_pkgname}"
}

package_gradle-doc() {
  pkgdesc+=' (documentation)'
  options=(
    '!strip'
  )
  cd \
    "${_tarname}/dist"
  install \
    -dm755 \
    "${pkgdir}/usr/share/java/${_pkgname}/docs"
  cp \
    -r \
    "docs/"* \
    "${pkgdir}/usr/share/java/${_pkgname}/docs"
}

package_gradle-src() {
  pkgdesc+=' (sources)'
  options=(
    '!strip'
  )
  cd \
    "${_tarname}/dist"
  install \
    -dm755 \
    "${pkgdir}/usr/share/java/${_pkgname}/src"
  cp \
    -r \
    "src/"* \
    "${pkgdir}/usr/share/java/${_pkgname}/src"
}

# vim: ts=2 sw=2 et:
