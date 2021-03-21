ARG PYTHON_VERSION='3.8.8'
ARG SCIPY_VERSION='1.6.*'

FROM python:${PYTHON_VERSION}-alpine as builder
ARG SCIPY_VERSION

RUN apk add gcc musl-dev

WORKDIR /root
RUN pip install -U pip setuptools wheel
RUN pip install https://github.com/rkhullar/pypi-alpine-wheels/blob/master/numpy/1.20.1/3.8.8/numpy-1.20.1-cp38-cp38-linux_x86_64.whl?raw=true
RUN pip wheel --no-binary :all: scipy==${SCIPY_VERSION}

## docker build -t scipy .
## docker run -it -v `pwd`/out:/root/out:rw scipy /bin/sh
