FROM debian:trixie-slim AS build

WORKDIR /src

ADD --checksum=sha256:7ade3d78d9403542cf53b3ed3a1575751c815d4b7ff3f1fb364aa1ee664807f0 \
 https://github.com/wojciech-graj/doom-ascii/archive/refs/tags/0.3.1.tar.gz .

RUN apt-get update \
 && apt-get upgrade -y \
 && apt-get install -y --no-install-recommends \
 build-essential \
 make \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

RUN tar xzf 0.3.1.tar.gz -C /src --strip-components=1 \
 && CFLAGS=-DDG_DEMO make -j$(nproc)

FROM debian:trixie-slim

RUN apt-get update \
 && apt-get upgrade -y \
 && apt-get install -y --no-install-recommends \
 inetutils-telnetd \
 openbsd-inetd \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

RUN echo "telnet stream tcp nowait root /usr/sbin/telnetd telnetd -E /usr/local/bin/launch-doom-ascii.sh" >> /etc/inetd.conf \
 && sed -i 's/^telnet[[:space:]]\+23\/tcp/telnet\t666\/tcp/' /etc/services

ADD --checksum=sha256:1d7d43be501e67d927e415e0b8f3e29c3bf33075e859721816f652a526cac771 \
 https://distro.ibiblio.org/slitaz/sources/packages/d/doom1.wad /doom-ascii/doom1.wad

COPY --from=build /src/_unix/game/doom-ascii /usr/local/bin/doom-ascii
COPY --from=build /src/_unix/game/.default.cfg /doom-ascii/.default.cfg

COPY launch-doom-ascii.sh /usr/local/bin/launch-doom-ascii.sh

EXPOSE 666

ENTRYPOINT ["inetd", "-i"]
