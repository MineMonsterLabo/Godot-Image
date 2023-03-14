FROM fedora:37

WORKDIR /root

# install base
RUN dnf -y install --setopt=install_weak_deps=False \
      bash bzip2 curl file findutils git make nano patch pkgconfig python3-pip unzip which xz && \
    pip install scons==4.3.0

# install linux build tool
RUN dnf -y install --setopt=install_weak_deps=False \
      pkgconfig libX11-devel libXcursor-devel libXrandr-devel libXinerama-devel \
      libXi-devel mesa-libGL-devel mesa-libGLU-devel alsa-lib-devel pulseaudio-libs-devel \
      libudev-devel yasm gcc-c++ libstdc++-static libatomic-static && \
    cert-sync /etc/pki/tls/certs/ca-bundle.crt

# install windows build tool
RUN dnf -y install --setopt=install_weak_deps=False \
      mingw32-gcc mingw32-gcc-c++ mingw32-winpthreads-static mingw64-gcc mingw64-gcc-c++ mingw64-winpthreads-static
      
# install mono
RUN PREFIX=/opt/mono && \
    VERSION=6.12.0.182 && \
    tar xvf mono-$VERSION.tar.xz && \
    cd mono-$VERSION && \
    ./configure --prefix=$PREFIX && \
    make && \
    make install
# RUN rpm --import "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF" && \
#     su -c 'curl https://download.mono-project.com/repo/centos8-stable.repo | tee /etc/yum.repos.d/mono-centos8-stable.repo' && \
#     dnf -y update && \
#     dnf -y install mono-complete

# install web build tool
RUN cd /opt && git clone https://github.com/emscripten-core/emsdk.git && \
    cd emsdk && \
    git pull && \
    ./emsdk install sdk-1.39.20-64bit && \
    ./emsdk activate sdk-1.39.20-64bit && \
    source ./emsdk_env.sh
# RUN chmod -R 0777 /opt/emsdk

COPY mono-windows/ /opt/mono-windows
# RUN chmod -R 0777 /opt/mono-windows

USER root
RUN update-alternatives --install /usr/bin/x86_64-w64-mingw32-gcc x86_64-w64-mingw32-gcc /usr/bin/x86_64-w64-mingw32-gcc-posix 90 && \
    update-alternatives --install /usr/bin/x86_64-w64-mingw32-g++ x86_64-w64-mingw32-g++ /usr/bin/x86_64-w64-mingw32-g++-posix 90

RUN mkdir /.config && \
    mkdir /.config/NuGet
COPY NuGet.Config /.config/NuGet/NuGet.Config
RUN chmod -R 0777 /.config

RUN mkdir /.nuget && \
    chmod -R 0777 /.nuget

RUN mkdir /.local && \
    chmod -R 0777 /.local
