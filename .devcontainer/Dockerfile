# Use the same base used in your workflow (rocker binder)
FROM ghcr.io/rocker-org/binder:latest

ARG QUARTO_VERSION=1.8.25
ENV DEBIAN_FRONTEND=noninteractive

# Build as root
USER root

# Minimal prerequisites + system libs similar to your GH Actions job
RUN apt-get update && apt-get install -y --no-install-recommends \
    apt-transport-https \
    ca-certificates \
    gnupg \
    dirmngr \
    wget \
    locales \
    libudunits2-dev \
    libpoppler-cpp-dev \
    gdal-bin \
    libgdal-dev \
    libgeos-dev \
    libproj-dev \
    libmagick++-dev \
    libxt6 \
    libxtst6 \
    libssl-dev \
    libprotobuf-dev \
    protobuf-compiler \
    cmake \
    poppler-utils \
    ghostscript \
    tk \
    libxml2-dev \
    libcurl4-openssl-dev \
    liblzma-dev \
    zlib1g-dev \
    texlive-latex-base \
    texlive-latex-recommended \
    texlive-latex-extra \
    texlive-fonts-recommended \
    texlive-fonts-extra \
    texlive-xetex \
    texlive-luatex \
    texlive-pictures \
    latexmk \
  && apt-get clean && rm -rf /var/lib/apt/lists/*

# Install Quarto CLI (pinned via build ARG)
RUN echo "Installing Quarto CLI v${QUARTO_VERSION}" \
  && ARCH=linux-amd64 \
  && FILE=quarto-${QUARTO_VERSION}-${ARCH}.deb \
  && URL="https://github.com/quarto-dev/quarto-cli/releases/download/v${QUARTO_VERSION}/${FILE}" \
  && wget -q --show-progress "$URL" -O "/tmp/${FILE}" \
  && dpkg -i "/tmp/${FILE}" || (apt-get update && apt-get -f install -y && dpkg -i "/tmp/${FILE}") \
  && rm -f "/tmp/${FILE}"

# Copy pip requirements and R installer from repo root (ensure build.context set to repo root)
COPY requirements.txt install.R /tmp/

# Install Python pip requirements
RUN python3 -m pip install --no-cache-dir --requirement /tmp/requirements.txt

# NOTE: Installing all R packages at build time can be slow. You can enable the next line
# to install packages during the Docker build, or run install.R during postCreate.
RUN Rscript /tmp/install.R

# Switch to jovyan user (rocker images use jovyan)
USER jovyan

WORKDIR /home/jovyan/work
