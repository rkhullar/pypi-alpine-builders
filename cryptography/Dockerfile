ARG PYTHON_VERSION='3.8.8'
ARG CRYPTOGRAPHY_VERSION='3.4.*'
ARG OPENSSL_VERSION='1.1.1j'

FROM python:${PYTHON_VERSION}-alpine as builder
ARG CRYPTOGRAPHY_VERSION
ARG OPENSSL_VERSION

# https://cryptography.io/en/latest/installation.html#alpine
RUN apk add gcc musl-dev python3-dev libffi-dev openssl-dev cargo
RUN apk add make perl

WORKDIR /root
RUN pip install -U pip setuptools wheel
RUN wget -O openssl.tar.gz https://www.openssl.org/source/openssl-${OPENSSL_VERSION}.tar.gz
RUN tar xvf openssl.tar.gz && rm openssl.tar.gz && mv openssl-* openssl

WORKDIR /root/openssl
RUN ./config no-shared no-ssl2 no-ssl3 -fPIC --prefix=/opt/openssl
RUN make
RUN make install

WORKDIR /root
ENV CFLAGS="-I/opt/openssl/include"
ENV LDFLAGS="-L/opt/openssl/lib"
RUN pip wheel --no-binary :all: cryptography==${CRYPTOGRAPHY_VERSION}

FROM python:${PYTHON_VERSION}-alpine
WORKDIR /root
COPY --from=builder /root/*.whl .
ENTRYPOINT ["/bin/sh"]

## docker build -t crypto .
## docker run -it -v `pwd`/out:/root/out:rw crypto /bin/sh
