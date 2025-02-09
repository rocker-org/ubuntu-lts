## Emacs, make this -*- mode: sh; -*-

FROM ubuntu:noble

LABEL org.label-schema.license="GPL-2.0" \
      org.label-schema.vcs-url="https://github.com/rocker-org/r-ubuntu" \
      org.label-schema.vendor="Rocker Project" \
      maintainer="Dirk Eddelbuettel <edd@debian.org>"

## Set a default user. Available via runtime flag `--user docker` 
## Add user to 'staff' group, granting them write privileges to /usr/local/lib/R/site.library
## User should also have & own a home directory (for rstudio or linked volumes to work properly). 
RUN useradd -s /bin/bash -m docker \
	&& usermod -a -G staff docker \
## Refresh apt, install minimal tools
        && apt update \
        && apt upgrade -y \
	&& apt install -y --no-install-recommends \
		ca-certificates \
                dirmngr \
                ed \
                gpg \
                gpg-agent \
		less \
		locales \
                lsb-release \
		vim-tiny \
## Install key and setup CRAN repo 
## Alternate: 'https://ppa.launchpadcontent.net/marutter/rrutter4.0/ubuntu noble' with key C8E868340AE02417DCF76B51F2B6E89C389782D0
        && gpg --keyserver keyserver.ubuntu.com --recv-keys 51716619E084DAB9 \
        && gpg --export --armor 51716619E084DAB9 > /etc/apt/trusted.gpg.d/cran_key.asc \
        && echo "deb [arch=amd64,arm64 signed-by=/etc/apt/trusted.gpg.d/cran_key.asc] https://cloud.r-project.org/bin/linux/ubuntu $(lsb_release -cs)-cran40/" > /etc/apt/sources.list.d/cran.list \
## Configure default locale, see https://github.com/rocker-org/rocker/issues/19
        && echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
	&& locale-gen en_US.utf8 \
	&& /usr/sbin/update-locale LANG=en_US.UTF-8

ENV LC_ALL=en_US.UTF-8
ENV LANG=en_US.UTF-8
## This was not needed before but we need it now
ENV DEBIAN_FRONTEND=noninteractive
## Otherwise timedatectl will get called which leads to 'no systemd' inside Docker
ENV TZ=UTC

# Now install R and littler, and create a link for littler in /usr/local/bin
# Default CRAN repo is now set by R itself, and littler knows about it too
RUN apt-get update \
        && apt-get install -y --no-install-recommends \
                 littler \
 		 r-base \
 		 r-base-dev \
 		 r-recommended \
                 r-cran-docopt \
	&& chown root:staff "/usr/local/lib/R/site-library" \
	&& chmod g+ws "/usr/local/lib/R/site-library" \
  	&& ln -s /usr/lib/R/site-library/littler/examples/install.r /usr/local/bin/install.r \
 	&& ln -s /usr/lib/R/site-library/littler/examples/install2.r /usr/local/bin/install2.r \
	&& ln -s /usr/lib/R/site-library/littler/examples/installBioc.r /usr/local/bin/installBioc.r \
 	&& ln -s /usr/lib/R/site-library/littler/examples/installGithub.r /usr/local/bin/installGithub.r \
 	&& ln -s /usr/lib/R/site-library/littler/examples/testInstalled.r /usr/local/bin/testInstalled.r \
  	&& ln -s /usr/lib/R/site-library/littler/examples/update.r /usr/local/bin/update.r \
 	&& rm -rf /tmp/downloaded_packages/ /tmp/*.rds \
 	&& rm -rf /var/lib/apt/lists/*

CMD ["bash"]
