ARG PYTHON_VERSION='3.8.8'
ARG NUMPY_VERSION='1.20.*'

FROM python:${PYTHON_VERSION}-alpine as builder
ARG NUMPY_VERSION

RUN apk add gcc gfortran musl-dev

WORKDIR /root
RUN pip install -U pip setuptools wheel
RUN pip wheel --no-binary :all: numpy==${NUMPY_VERSION}

## docker build -t numpy .
## docker run -it -v `pwd`/out:/root/out:rw numpy /bin/sh
