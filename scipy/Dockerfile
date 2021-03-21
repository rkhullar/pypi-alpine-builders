ARG PYTHON_VERSION='3.8.8'
ARG SCIPY_VERSION='1.6.*'

FROM python:${PYTHON_VERSION}-alpine as builder
ARG SCIPY_VERSION

RUN apk add g++ gfortran musl-dev patchelf openblas-dev lapack-dev

WORKDIR /root
RUN pip install -U pip setuptools wheel
#RUN pip install https://github.com/rkhullar/pypi-alpine-wheels/blob/master/numpy/1.20.1/3.8.8/numpy-1.20.1-cp38-cp38-linux_x86_64.whl?raw=true
RUN pip wheel --no-binary :all: scipy==${SCIPY_VERSION}

# https://github.com/MacPython/numpy-wheels
RUN pip install auditwheel
COPY policy.json .
RUN PYTHON_MAJOR=`python -c "import sys; v=sys.version_info; print(f'{v.major}.{v.minor}')"` \
  && SITE_PACKAGES=/usr/local/lib/python${PYTHON_MAJOR}/site-packages                        \
  && cp policy.json ${SITE_PACKAGES}/auditwheel/policy/policy.json                           \
  && rm policy.json

RUN auditwheel -v show numpy-*.whl
RUN auditwheel -v show scipy-*.whl
RUN auditwheel repair numpy-*.whl
RUN auditwheel repair scipy-*.whl

FROM python:${PYTHON_VERSION}-alpine
WORKDIR /root
COPY --from=builder /root/wheelhouse/*.whl .
ENTRYPOINT ["/bin/sh"]

## docker build -t scipy .
## docker run -it -v `pwd`/out:/root/out:rw scipy
