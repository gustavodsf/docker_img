
## Emacs, make this -*- mode: sh; -*-

FROM databricksruntime/minimal:latest

LABEL org.label-schema.license="GPL-2.0" \
      org.label-schema.vcs-url="https://github.com/rocker-org/r-ubuntu" \
      org.label-schema.vendor="Rocker Project" \
      maintainer="Dirk Eddelbuettel <edd@debian.org>"

## Set a default user. Available via runtime flag `--user docker` 
## Add user to 'staff' group, granting them write privileges to /usr/local/lib/R/site.library
## User should also have & own a home directory (for rstudio or linked volumes to work properly). 
RUN useradd docker \
	&& mkdir /home/docker \
	&& chown docker:docker /home/docker \
	&& addgroup docker staff

RUN apt-get update \
	&& apt-get install -y --no-install-recommends \
		software-properties-common \
                dirmngr \
                ed \
		less \
		locales \
		vim-tiny \
		wget \
		ca-certificates \
        && add-apt-repository --enable-source --yes "ppa:marutter/rrutter4.0" \
        && add-apt-repository --enable-source --yes "ppa:c2d4u.team/c2d4u4.0+"

## Configure default locale, see https://github.com/rocker-org/rocker/issues/19
RUN echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
	&& locale-gen en_US.utf8 \
	&& /usr/sbin/update-locale LANG=en_US.UTF-8

ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8

## This was not needed before but we need it now
ENV DEBIAN_FRONTEND noninteractive

## Otherwise timedatectl will get called which leads to 'no systemd' inside Docker
ENV TZ UTC

# Now install R and littler, and create a link for littler in /usr/local/bin
# Default CRAN repo is now set by R itself, and littler knows about it too
# r-cran-docopt is not currently in c2d4u so we install from source
RUN apt-get update \
        && apt-get install -y --no-install-recommends \
                 littler \
 		 r-base \
 		 r-base-dev \
 		 r-recommended \
  	&& ln -s /usr/lib/R/site-library/littler/examples/install.r /usr/local/bin/install.r \
 	&& ln -s /usr/lib/R/site-library/littler/examples/install2.r /usr/local/bin/install2.r \
 	&& ln -s /usr/lib/R/site-library/littler/examples/installGithub.r /usr/local/bin/installGithub.r \
 	&& ln -s /usr/lib/R/site-library/littler/examples/testInstalled.r /usr/local/bin/testInstalled.r \
 	&& install.r docopt \
 	&& rm -rf /tmp/downloaded_packages/ /tmp/*.rds \
 	&& rm -rf /var/lib/apt/lists/*

RUN apt-get update \
   && apt-get install -y libnetcdf-dev \
   && apt-get install -y libxml2-dev libcurl4-openssl-dev libssl-dev libv8-dev \
   && apt-get install -y libproj-dev \
   && apt-get install -y libudunits2-0 libudunits2-dev \
   && apt-get install -y gdal-bin proj-bin libgdal-dev libproj-dev \
   && apt-get install -y libcgal-dev libglu1-mesa-dev libglu1-mesa-dev \
   && apt-get install -y libx11-dev \
   && apt-get install -y libnetcdf-*

# hwriterPlus is used by Databricks to display output in notebook cells
# Rserve allows Spark to communicate with a local R process to run R code
RUN R -e "install.packages(c('hwriterPlus'), repos='https://mran.revolutionanalytics.com/snapshot/2017-02-26')" \
 && R -e "install.packages(c('htmltools'), repos='https://cran.microsoft.com/')" \
 && R -e "install.packages('Rserve', repos='http://rforge.net/')"

RUN echo "options(repos = c(CRAN = 'https://cran.rstudio.com'))" >.Rprofile

RUN R -e "install.packages('remotes');" --silent --no-echo \
		&& R -e "remotes::install_version('ncdf4', '1.17')" --silent --no-echo \
		&& R -e "remotes::install_version('reshape', '0.8.8')" --silent --no-echo  \
		&& R -e "remotes::install_version('reshape2', '1.4.4')" --silent --no-echo  \
		&& R -e "remotes::install_version('flock', '0.7')" --silent --no-echo  \
		&& R -e "remotes::install_version('rasterVis', '0.49')" --silent --no-echo  \
		&& R -e "remotes::install_version('furrr', '0.2.1')" --silent --no-echo  \
		&& R -e "remotes::install_version('here', '1.0.0')"  \
		&& R -e "remotes::install_version('dplyr', '1.0.2')"  \
		&& R -e "remotes::install_version('tidyverse', '1.3.0')"  \
		&& R -e "remotes::install_version('sf', '0.9-6')"  \
		&& R -e "remotes::install_version('knitr', '1.30')"  \
		&& R -e "remotes::install_version('Matrix', '1.2-18')"  \
		&& R -e "remotes::install_version('rgl', '0.100.054')"  \
		&& R -e "remotes::install_version('rgdal', '1.5-18')"  \
		&& R -e "remotes::install_version('plot3D', '1.3')"  \
		&& R -e "remotes::install_version('future', '1.21.0')"  \
		&& R -e "remotes::install_version('gridExtra', '2.3')"
