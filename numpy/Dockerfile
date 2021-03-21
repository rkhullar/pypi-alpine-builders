ARG PYTHON_VERSION='3.8.8'
ARG NUMPY_VERSION='1.20.*'
ARG OPENBLAS_VERSION='0.3.13'

FROM python:${PYTHON_VERSION}-alpine as builder
ARG NUMPY_VERSION
ARG OPENBLAS_VERSION

RUN apk add gcc gfortran musl-dev make perl linux-headers patchelf

WORKDIR /root
RUN pip install -U pip setuptools wheel
RUN wget -O openblas.tar.gz https://github.com/xianyi/OpenBLAS/archive/v${OPENBLAS_VERSION}.tar.gz
RUN tar xvf openblas.tar.gz && rm openblas.tar.gz && mv OpenBLAS-* openblas

WORKDIR /root/openblas
# https://github.com/slothai/docker-openblas
RUN make CC=gcc FC=gfortran BINARY=64 USE_THREAD=1 NO_AFFINITY=1 USE_OPENMP=1
RUN make PREFIX=/opt/openblas install

WORKDIR /root
COPY setup.cfg .numpy-site.cfg
RUN pip wheel --no-binary :all: numpy==${NUMPY_VERSION}

# https://github.com/MacPython/numpy-wheels
RUN pip install auditwheel
COPY policy.json .
RUN PYTHON_MAJOR_MINOR=`python -c "import sys; v=sys.version_info; print(f'{v.major}.{v.minor}')"` \
  && SITE_PACKAGES=/usr/local/lib/python${PYTHON_MAJOR_MINOR}/site-packages                        \
  && cp policy.json ${SITE_PACKAGES}/auditwheel/policy/policy.json                                 \
  && rm policy.json

# ENV LD_LIBRARY_PATH=/opt/openblas/lib
RUN auditwheel -v show numpy-*.whl
RUN auditwheel repair numpy-*.whl

FROM python:${PYTHON_VERSION}-alpine
WORKDIR /root
COPY --from=builder /root/wheelhouse/*.whl .
ENTRYPOINT ["/bin/sh"]

## docker build -t numpy .
## docker run -it -v `pwd`/out:/root/out:rw numpy
## docker run -it -v `pwd`/out:/root/out:rw python:3.8.8-alpine /bin/sh
