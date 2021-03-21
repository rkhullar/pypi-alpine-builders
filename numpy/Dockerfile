ARG PYTHON_VERSION='3.8.8'
ARG NUMPY_VERSION='1.20.*'

FROM python:${PYTHON_VERSION}-alpine as builder
ARG NUMPY_VERSION

WORKDIR /root
RUN apk add gcc gfortran musl-dev patchelf openblas-dev lapack-dev
RUN pip install -U pip setuptools wheel
RUN pip wheel --no-binary :all: numpy==${NUMPY_VERSION}

# https://github.com/MacPython/numpy-wheels
RUN pip install auditwheel
COPY policy.json .
RUN PYTHON_MAJOR=`python -c "import sys; v=sys.version_info; print(f'{v.major}.{v.minor}')"` \
  && SITE_PACKAGES=/usr/local/lib/python${PYTHON_MAJOR}/site-packages                        \
  && cp policy.json ${SITE_PACKAGES}/auditwheel/policy/policy.json                           \
  && rm policy.json

RUN auditwheel -v show numpy-*.whl
RUN auditwheel repair numpy-*.whl

FROM python:${PYTHON_VERSION}-alpine
WORKDIR /root
COPY --from=builder /root/wheelhouse/*.whl .
ENTRYPOINT ["/bin/sh"]

## docker build -t numpy .
## docker run -it -v `pwd`/out:/root/out:rw numpy
## docker run -it -v `pwd`/out:/root/out:rw python:3.8.8-alpine /bin/sh
