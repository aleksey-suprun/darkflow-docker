FROM python:3.5

RUN pip3 install numpy cython tensorflow

RUN apt-get update -y \
    && apt-get install -y \
        cmake \
        g++

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

RUN wget --no-check-certificate -O darkflow.zip https://github.com/thtrieu/darkflow/archive/master.zip \
    && unzip darkflow.zip \
    && mv darkflow-master darkflow \
    && rm -rf darkflow/test \
    && rm -rf darkflow/sample_img \
    && rm darkflow/labels.txt \
    && rm darkflow.zip

WORKDIR /darkflow

RUN python3 setup.py build_ext --inplace

# ENTRYPOINT ["python3", "flow"]
ENTRYPOINT ["python3"]
