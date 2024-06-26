FROM ubuntu:16.04
# minimal docker file to get sp and sf running on ubunty 16.04 image,
# using gdal/geos/proj from ppa:ubuntugis/ubuntugis-unstable

MAINTAINER "edzerpebesma" edzer.pebesma@uni-muenster.de

RUN apt-get update && apt-get install -y software-properties-common
RUN add-apt-repository ppa:ubuntugis/ubuntugis-unstable

RUN echo "deb https://cran.rstudio.com/bin/linux/ubuntu xenial/  " >> /etc/apt/sources.list
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E084DAB9

RUN apt-get update
RUN apt-get upgrade -y

RUN export DEBIAN_FRONTEND=noninteractive; apt-get -y update \
  && apt-get install -y \
	libcurl4-openssl-dev \
	qpdf \
	pandoc \
	make \
	wget \
	git \
	libudunits2-dev \
	libgdal-dev \
	libgeos-dev \
	libproj-dev \
	r-base-dev \
	gdb \
	valgrind \
	vim

RUN apt-get install -y texinfo \
       texlive-base \
       texlive-extra-utils \
       texlive-fonts-extra \
       texlive-fonts-recommended \
       texlive-generic-recommended \
       texlive-latex-base \
       texlive-latex-extra \
       texlive-latex-recommended

# stuff for the tmaptools/rmapshaper/geojsonio etc stack:
RUN apt-get install -y libv8-3.14-dev libprotobuf-dev protobuf-compiler libcairo2-dev pandoc pandoc-citeproc 
RUN add-apt-repository -y ppa:opencpu/jq
RUN apt-get update
RUN apt-get install -y libjq-dev

## Check out & build R-devel:
RUN apt-get install -y subversion

RUN cd /tmp && svn co https://svn.r-project.org/R/trunk R-devel

RUN /tmp/R-devel/tools/rsync-recommended
## Build and install according the standard 'recipe' I emailed/posted years ago

RUN apt-get install -y libreadline-dev libbz2-dev

RUN cd /tmp/R-devel \
	&& R_PAPERSIZE=letter \
		R_BATCHSAVE="--no-save --no-restore" \
		R_BROWSER=xdg-open \
		PAGER=/usr/bin/pager \
		PERL=/usr/bin/perl \
		R_UNZIPCMD=/usr/bin/unzip \
		R_ZIPCMD=/usr/bin/zip \
		R_PRINTCMD=/usr/bin/lpr \
		LIBnn=lib \
		AWK=/usr/bin/awk \
		CFLAGS=$(R CMD config CFLAGS) \
		CXXFLAGS=$(R CMD config CXXFLAGS) \
	./configure --enable-R-shlib \
               --without-blas \
               --without-lapack \
               --with-readline \
               --with-recommended-packages \
               --program-suffix=dev \
			   --with-x=no \
	&& make \
	&& make install \
	&& rm -rf /tmp/R-devel

## Set default CRAN repo
RUN echo 'options(repos = c(CRAN = "https://cran.rstudio.com/"), download.file.method = "libcurl")' >> /usr/local/lib/R/etc/Rprofile.site

RUN Rscript -e 'install.packages(c("CFtime", sf", "lwgeom", "covr", "raster", "rgdal", "Rcpp", "magrittr", "abind", "units", "ggplot2", "ggthemes", "viridis", "testthat", "knitr", "covr", "rmarkdown", "PCICt", "ggforce", "gstat", "pbapply", "plm", "spacetime", "xts"), dependencies = TRUE, repos = "https://cloud.r-project.org")'

RUN Rscript -e 'install.packages("starsdata", repos = "http://pebesma.staff.ifgi.de/")'

RUN git clone https://github.com/r-spatial/stars.git

RUN R CMD build --no-manual stars

#RUN R CMD check --no-build-vignettes --no-manual --as-cran --run-dontrun sf_*tar.gz
RUN R CMD check --no-manual stars_*tar.gz

CMD ["/bin/bash"]
