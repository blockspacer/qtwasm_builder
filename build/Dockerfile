FROM trzeci/emscripten AS qtbuilder

RUN mkdir -p /development
WORKDIR /development

RUN git clone --branch=5.13 git://code.qt.io/qt/qt5.git

WORKDIR /development/qt5

### TODO: For WebAssembly we should not need a full init-repository. However, this way we
#   can easily track changes.
RUN ./init-repository --berlin

RUN mkdir -p /development/qt5_build
WORKDIR /development/qt5_build

RUN /development/qt5/configure -xplatform wasm-emscripten -nomake examples -nomake tests -opensource --confirm-license
RUN make -j `grep -c '^processor' /proc/cpuinfo`
RUN make install

# Construct the build image from user perspective
FROM trzeci/emscripten AS userbuild

COPY --from=qtbuilder /usr/local/Qt-5.13.0/ /usr/local/Qt-5.13.0/
ENV PATH="/usr/local/Qt-5.13.0/bin:${PATH}"

WORKDIR /project/build
CMD qmake /project/source && make -j `grep -c '^processor' /proc/cpuinfo`
