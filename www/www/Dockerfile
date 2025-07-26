FROM debian:trixie-slim AS build

ADD --checksum=sha256:26518e14a3a04100cd76c0d96cab2d1171f36152215edd9790a28d20268200c1 \
 https://github.com/plantuml/plantuml/releases/download/v1.2025.4/plantuml-1.2025.4.jar /usr/share/java/plantuml-1.2025.4.jar

ADD https://github.com/wojciech-graj/.emacs.d.git /root/.emacs.d

RUN apt-get update \
 && apt-get upgrade -y \
 && xargs -a /root/.emacs.d/packages.txt apt-get install -y --no-install-recommends \
 && apt-get install -y --no-install-recommends \
 emacs-nox \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

RUN emacs --batch --script /root/.emacs.d/init.el

COPY --from=src . /src/w-graj.net

RUN WEBSITE_BASE_DIRECTORY=/src/w-graj.net \
 WEBSITE_PUBLISHING_DIRECTORY=/src/out.w-graj.net \
 emacs --batch \
 --script /root/.emacs.d/init.el \
 --execute "(setq enable-local-variables :all)" \
 --execute "(org-publish-project \"website\")"

FROM httpd:2-bookworm

COPY --from=res . /usr/local/apache2/htdocs
COPY --from=build /src/out.w-graj.net /usr/local/apache2/htdocs
COPY httpd.conf /usr/local/apache2/conf/httpd.conf
COPY httpd-custom.conf /usr/local/apache2/conf/extra/httpd-custom.conf
