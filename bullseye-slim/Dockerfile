FROM debian:bullseye-slim

ARG GRAALVM_VERSION=21.2.0
ARG JAVA_VERSION=11
ARG GRAALVM_PKG=graalvm-ce-java${JAVA_VERSION}-linux-GRAALVM_ARCH-${GRAALVM_VERSION}.tar.gz
ARG GRAALVM_URL_ROOT=https://github.com/graalvm/graalvm-ce-builds/releases/download/vm-${GRAALVM_VERSION}/
ARG TARGETPLATFORM

RUN set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends \
    ca-certificates p11-kit \
    curl \
    ; \
    rm -rf /var/lib/apt/lists/*

ENV LANG=en_US.UTF-8 \
    JAVA_HOME=/opt/graalvm-ce-java${JAVA_VERSION}-${GRAALVM_VERSION}/

COPY gu-wrapper.sh /usr/local/bin/gu

ENV GRAALVM_AMD64_SHA256=bbd3e03025168172a76c2a29e6a14c1c37e3476b30774259c3ef5952fb86f470
ENV GRAALVM_ARM64_SHA256=bbdf38d5e6871f7e3b2470ab9b9bb760667d4524ee2a20eadfaf13636a2d018c

RUN set -eux; \
    dpkgArch="$(dpkg --print-architecture)"; \
    GRAALVM_PKG=$(printf '%s\n' "$GRAALVM_PKG" | sed "s/GRAALVM_ARCH/${dpkgArch}/"); \
    curl --fail --silent --location --retry 3 --remote-name ${GRAALVM_URL_ROOT}${GRAALVM_PKG}; \
    if [ "$dpkgArch" = "amd64" ]; then \
      echo "$GRAALVM_AMD64_SHA256 *${GRAALVM_PKG}" | sha256sum --strict --check; \
    fi; \
    if [ "$dpkgArch" = "arm64" ]; then \
      echo "$GRAALVM_ARM64_SHA256 *${GRAALVM_PKG}" | sha256sum --strict --check; \
    fi; \
    tar -xzC /opt/ -f ${GRAALVM_PKG}; \
    mkdir -p "/usr/java"; \
    ln -sfT "$JAVA_HOME" /usr/java/default; \
    ln -sfT "$JAVA_HOME" /usr/java/latest; \
    for bin in "${JAVA_HOME}/bin/"*; do \
      base="$(basename "$bin")"; \
      [ ! -e "/usr/bin/$base" ]; \
      update-alternatives --install "/usr/bin/$base" "$base" "$bin" 20000; \
    done; \
    chmod +x /usr/local/bin/gu

RUN gu install native-image

CMD ["jshell"]
