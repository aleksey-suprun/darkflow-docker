FROM debian:stretch AS xbuild

ENV RESIN_XBUILD_URL https://github.com/aleksey-suprun/resin-xbuild/releases/download/v0.1.0/resin-xbuild-0.1.0.tar.gz
ENV QEMU_URL https://github.com/resin-io/qemu/releases/download/v2.9.0%2Bresin1/qemu-2.9.0.resin1-arm.tar.gz

ADD $QEMU_URL /xbuild/qemu.tar.gz
ADD $RESIN_XBUILD_URL /xbuild/resin-xbuild.tar.gz

RUN mkdir /xbuild/bin
RUN tar --strip-components=1 -C /xbuild/bin -xzf /xbuild/resin-xbuild.tar.gz
RUN tar --strip-components=1 -C /xbuild/bin -xzf /xbuild/qemu.tar.gz


FROM arm32v7/python:3.5-stretch

COPY --from=xbuild /xbuild/bin/ /usr/bin/

RUN [ "cross-build-start" ]

RUN pip3 install numpy cython

RUN apt-get update -y \
    && apt-get install -y \
        cmake \
        g++ \
        libatlas-base-dev \
        libhdf5-serial-dev

RUN mkdir /opencv \
    && cd /opencv \
    && wget --no-check-certificate -O opencv-src.tar.gz https://github.com/opencv/opencv/archive/4.1.0.tar.gz \
    && tar -xzf opencv-src.tar.gz --strip-components=1 \
    && rm opencv-src.tar.gz \
    && mkdir build && cd build \
    && cmake \
        -D CMAKE_BUILD_TYPE=Release \
        -D CMAKE_INSTALL_PREFIX=/usr/local \
        -D BUILD_SHARED_LIBS=OFF \
        -D BUILD_EXAMPLES=OFF \
        -D BUILD_TESTS=OFF \
        -D BUILD_PERF_TESTS=OFF \
        .. \
    && make -j$(nproc) \
    && make install \
    && cd / \
    && rm -rf /opencv

COPY pip.conf /etc/pip.conf

# `RUN pip3 install tensorflow` does not work. For more info see the link:
# https://stackoverflow.com/questions/56002315/undefined-symbol-pythreadstate-current-when-importing-tensorflow/56277943#56277943
RUN pip3 install https://github.com/lhelontra/tensorflow-on-arm/releases/download/v1.13.1/tensorflow-1.13.1-cp35-none-linux_armv7l.whl

RUN wget --no-check-certificate -O darkflow.zip https://github.com/thtrieu/darkflow/archive/master.zip \
    && unzip darkflow.zip \
    && mv darkflow-master darkflow \
    && rm -rf darkflow/test \
    && rm -rf darkflow/sample_img \
    && rm darkflow/labels.txt \
    && rm darkflow.zip

WORKDIR /darkflow

RUN python3 setup.py build_ext --inplace

RUN [ "cross-build-end" ]

# ENTRYPOINT ["python3", "flow"]
ENTRYPOINT ["python3"]
