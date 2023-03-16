FROM fedora:37

USER root
WORKDIR /root

# install base
RUN dnf -y install --setopt=install_weak_deps=False \
      bash bzip2 curl file findutils git make nano patch pkgconfig python3-pip unzip which xz && \
    pip install scons==4.3.0

# install linux build tool
RUN dnf -y install --setopt=install_weak_deps=False \
      pkgconfig libX11-devel libXcursor-devel libXrandr-devel libXinerama-devel \
      libXi-devel mesa-libGL-devel mesa-libGLU-devel alsa-lib-devel pulseaudio-libs-devel \
      libudev-devel yasm gcc-c++ libstdc++-static libatomic-static
#    cert-sync /etc/pki/tls/certs/ca-bundle.crt

# install windows build tool
RUN dnf -y install --setopt=install_weak_deps=False \
      mingw32-gcc mingw32-gcc-c++ mingw32-winpthreads-static mingw64-gcc mingw64-gcc-c++ mingw64-winpthreads-static
      
# install mono
RUN rpm -ivh --nodeps \
      https://download.mono-project.com/repo/centos8-preview/m/msbuild/msbuild-16.10.1+xamarinxplat.2021.05.26.14.00-0.xamarin.7.epel8.noarch.rpm \
      https://download.mono-project.com/repo/centos8-preview/m/msbuild/msbuild-sdkresolver-16.10.1+xamarinxplat.2021.05.26.14.00-0.xamarin.7.epel8.noarch.rpm \
      https://download.mono-project.com/repo/centos8-preview/m/msbuild-libhostfxr/msbuild-libhostfxr-3.0.0.2019.04.16.02.13-0.xamarin.4.epel8.x86_64.rpm \
      https://download.mono-project.com/repo/centos8-preview/n/nuget/nuget-5.6.0.6489.bin-0.xamarin.1.epel8.noarch.rpm

# install web build tool
RUN cd /opt && git clone https://github.com/emscripten-core/emsdk.git && \
    cd emsdk && \
    git pull && \
    ./emsdk install sdk-1.39.20-64bit && \
    ./emsdk activate sdk-1.39.20-64bit && \
    source ./emsdk_env.sh
RUN cp /opt/emsdk/.emscripten .emscripten && chmod 0777 .emscripten
RUN chmod -R 0777 /opt/emsdk

RUN dnf -y install --setopt=install_weak_deps=False \
      autoconf libtool automake cmake gettext && \
    git clone -b 'mono-6.12.0.182' --single-branch --progress --depth 1 'https://github.com/mono/mono' && \
    cd mono && \
    git submodule update --init --recursive --recommend-shallow -j 6 --progress && \
    cd ../ && \
    git clone --progress https://github.com/godotengine/godot-mono-builds && \
    cd godot-mono-builds && \
    git checkout 8767196960fb884ae95f337ee10cb131ae720086 && \
    python3 patch_mono.py --mono-sources /root/mono && \
    python3 linux.py configure --target=x86_64 --mono-sources /root/mono && \
    python3 linux.py make --target=x86_64 --mono-sources /root/mono && \
    python3 patch_emscripten.py --mono-sources /root/mono && \
    python3 wasm.py configure --target=runtime --mono-sources /root/mono && \
    python3 wasm.py make --target=runtime --mono-sources /root/mono
# RUN rpm --import "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF" && \
#     su -c 'curl https://download.mono-project.com/repo/centos8-stable.repo | tee /etc/yum.repos.d/mono-centos8-stable.repo' && \
#     dnf -y update && \
#     dnf -y install mono-complete
# RUN chmod -R 0777 /opt/mono

COPY mono-windows/ /opt/mono-windows
RUN chmod -R 0777 /opt/mono-windows

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
