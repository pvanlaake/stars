FROM ubuntu:14.04
# minimal docker file to get sp and sf running on trusty images
# using gdal/geos/proj from ppa:ubuntugis/ubuntugis-unstable

MAINTAINER "edzerpebesma" edzer.pebesma@uni-muenster.de

RUN apt-get update && apt-get install -y software-properties-common
RUN add-apt-repository ppa:ubuntugis/ubuntugis-unstable

RUN echo "deb https://cran.rstudio.com/bin/linux/ubuntu trusty/  " >> /etc/apt/sources.list
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

RUN Rscript -e 'install.packages(c("Rcpp", "magrittr", "abind", "units", "sf", "ggplot2", "ggthemes", "viridis", "testthat", "knitr", "covr", "rmarkdown"), repos = "https://cloud.r-project.org")'

RUN git clone https://github.com/r-spatial/stars.git

RUN export DEBIAN_FRONTEND=noninteractive; apt-get install -y pandoc-citeproc

RUN Rscript -e 'install.packages(c("dplyr"), repos = "https://cloud.r-project.org")'

RUN R CMD build --no-build-vignettes --no-manual stars

RUN Rscript -e 'install.packages(c("CFtime", PCICt","RNetCDF","ggforce","gstat","lwgeom","maps","ncmeta","pbapply","plm","raster","sp","spacetime","zoo","xts"), repos = "https://cloud.r-project.org")'


#RUN R CMD check --no-build-vignettes --no-manual --as-cran --run-dontrun sf_*tar.gz
RUN _R_CHECK_FORCE_SUGGESTS_=false R CMD check --no-manual stars_*tar.gz

CMD ["/bin/bash"]
