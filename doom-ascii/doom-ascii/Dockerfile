FROM debian:trixie-slim AS build

WORKDIR /src

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
 build-essential \
 make \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

ADD --checksum=sha256:a7cd1936550cc862bda6029860a9dd3caf73d14d7e6c29e56ae807b234a30f82 \
 https://github.com/wojciech-graj/doom-ascii/archive/refs/tags/v0.3.2.tar.gz .

RUN tar xzf v0.3.2.tar.gz -C /src --strip-components=1 \
 && CFLAGS=-DDG_DEMO make -j"$(nproc)"

FROM debian:trixie-slim

RUN apt-get update \
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
