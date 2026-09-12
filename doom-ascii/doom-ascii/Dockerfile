FROM debian:trixie-slim AS build

WORKDIR /src

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
 build-essential \
 make \
 && rm -rf /var/lib/apt/lists/*

ADD --checksum=sha256:a7cd1936550cc862bda6029860a9dd3caf73d14d7e6c29e56ae807b234a30f82 \
 https://github.com/wojciech-graj/doom-ascii/archive/refs/tags/v0.3.2.tar.gz .

RUN tar xzf v0.3.2.tar.gz -C /src --strip-components=1 \
 && CFLAGS=-DDG_DEMO make -j"$(nproc)"

FROM debian:trixie-slim AS wad

ADD --checksum=sha256:99ce219867b28c3e467b0ee84f5059f4c610f46bba957278584f50fa9c3753a0 \
 https://archive.org/download/wadarchive/DATA/5b.zip/5b%2F2e249b9c5133ec987b3ea77596381dc0d6bc1d%2F5b2e249b9c5133ec987b3ea77596381dc0d6bc1d.wad.gz \
 doom1.wad.gz

RUN gunzip doom1.wad.gz

FROM debian:trixie-slim

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
 inetutils-telnetd \
 openbsd-inetd \
 && rm -rf /var/lib/apt/lists/*

RUN echo "telnet stream tcp nowait root /usr/sbin/telnetd telnetd -E /usr/local/bin/launch-doom-ascii.sh" >> /etc/inetd.conf \
 && sed -i 's/^telnet[[:space:]]\+23\/tcp/telnet\t666\/tcp/' /etc/services

COPY --from=wad /doom1.wad /doom-ascii/doom1.wad
COPY --from=build /src/_unix/game/doom-ascii /usr/local/bin/doom-ascii
COPY --from=build /src/_unix/game/.default.cfg /doom-ascii/.default.cfg

COPY launch-doom-ascii.sh /usr/local/bin/launch-doom-ascii.sh

EXPOSE 666

ENTRYPOINT ["inetd", "-i"]
