FROM public.ecr.aws/docker/library/python:3.10-slim-buster

ENV PYTHONUNBUFFERED=TRUE
ENV DEBIAN_FRONTEND noninteractive
ENV CONDA_DIR="/opt/conda"
ENV PATH="${CONDA_DIR}/bin:${PATH}"

RUN apt-get update \
  && apt-get install -y bzip2 gcc wget \
  && rm -rf /var/lib/apt/lists/*

RUN arch=$(uname -m) && \
    if [ "${arch}" = "x86_64" ]; then \
        arch="64"; \
    fi && \
    wget -qO- https://anaconda.org/conda-forge/micromamba/0.19.1/download/linux-${arch}/micromamba-0.19.1-0.tar.bz2 | tar -xvj bin/micromamba --strip-components=1

RUN mkdir -p "${CONDA_DIR}" && \
    mv micromamba /bin/micromamba


RUN wget -O /usr/local/bin/dumb-init https://github.com/Yelp/dumb-init/releases/download/v1.2.0/dumb-init_1.2.0_amd64
RUN chmod +x /usr/local/bin/dumb-init

RUN micromamba install --root-prefix="${CONDA_DIR}" --prefix="${CONDA_DIR}" \
    -y -c conda-forge \
    python==3.10.12 \
    python-blosc==1.11.1 \
    pip \
    dask==2024.4.1 \
    lz4==4.3.3 \
    nomkl==1.0 \
    msgpack-python==1.0.7 \
    netcdf4==1.6.5 \
    numpy \
    pandas \
    xarray==2023.10.1 \
    bokeh==3.4.0 \
    s3fs==2024.3.1 \
    fsspec==2024.3.1 \
    h5netcdf==1.3.0 \
    distributed==2024.4.1 \
    tornado==6.4 \ 
    cloudpickle==3.0.0 \
    h5py==3.10.0 \
    zarr==2.17.2 \
    rechunker==0.5.2 \
    intake-esm==2024.2.6 \
    intake==0.7.0 \
    && find /opt/conda/ -type f,l -name '*.a' -delete \
    && find /opt/conda/ -type f,l -name '*.pyc' -delete \
    && find /opt/conda/ -type f,l -name '*.js.map' -delete \
    && find /opt/conda/lib/python*/site-packages/bokeh/server/static -type f,l -name '*.js' -not -name '*.min.js' -delete \
    && rm -rf /opt/conda/pkgs

# RUN pip install botocore==1.34.51 aiobotocore==2.12.2 boto3==1.34.51 rechunker==0.5.2 ipykernel==6.29.4 dask==2024.4.1 distributed==2024.4.1 tornado==6.4 cloudpickle==3.0.0 msgpack==1.0.8 numpy lz4==4.3.3 netcdf4==1.6.5 xarray==2023.10.1 s3fs==2024.3.1 fsspec==2024.3.1 h5netcdf==1.3.0 h5py==3.10.0 intake-esm==2024.2.6 intake==0.7.0 icclim==6.5.0 zarr==2.17.2

COPY prepare.sh /usr/bin/prepare.sh
RUN chmod +x /usr/bin/prepare.sh

RUN mkdir /opt/app /etc/dask
COPY dask.yaml /etc/dask/

ENTRYPOINT ["/usr/local/bin/dumb-init", "/usr/bin/prepare.sh"]
