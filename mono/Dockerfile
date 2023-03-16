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

# install web build tool
RUN cd /opt && git clone https://github.com/emscripten-core/emsdk.git && \
    cd emsdk && \
    git pull && \
    ./emsdk install sdk-1.39.20-64bit && \
    ./emsdk activate sdk-1.39.20-64bit && \
    source ./emsdk_env.sh
RUN cp /opt/emsdk/.emscripten .emscripten && chmod 0777 .emscripten
RUN chmod -R 0777 /opt/emsdk

# install mono
# RUN rpm -ivh --nodeps \
#       https://download.mono-project.com/repo/centos8-preview/m/msbuild/msbuild-16.10.1+xamarinxplat.2021.05.26.14.00-0.xamarin.7.epel8.noarch.rpm \
#       https://download.mono-project.com/repo/centos8-preview/m/msbuild/msbuild-sdkresolver-16.10.1+xamarinxplat.2021.05.26.14.00-0.xamarin.7.epel8.noarch.rpm \
#       https://download.mono-project.com/repo/centos8-preview/m/msbuild-libhostfxr/msbuild-libhostfxr-3.0.0.2019.04.16.02.13-0.xamarin.4.epel8.x86_64.rpm \
#       https://download.mono-project.com/repo/centos8-preview/n/nuget/nuget-5.6.0.6489.bin-0.xamarin.1.epel8.noarch.rpm

RUN rpm --import "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF" && \
    su -c 'curl https://download.mono-project.com/repo/centos8-stable.repo | tee /etc/yum.repos.d/mono-centos8-stable.repo' && \
    dnf update && \
    dnf -y install --setopt=install_weak_deps=False mono-complete && \
    curl -OL https://github.com/godotengine/godot-mono-builds/releases/download/release-8767196/linux-x86_64.zip && \
    curl -OL https://github.com/godotengine/godot-mono-builds/releases/download/release-8767196/wasm-runtime-threads.zip && \
#     curl -OL https://github.com/godotengine/godot-mono-builds/releases/download/release-8767196/windows-x86_64.zip && \
    curl -OL https://github.com/godotengine/godot-mono-builds/releases/download/release-8767196/bcl-desktop.zip && \
    curl -OL https://github.com/godotengine/godot-mono-builds/releases/download/release-8767196/bcl-wasm.zip && \
    mkdir /opt/mono && mkdir mono-bcl && \
    unzip -d /opt/mono/linux-x86_64 linux-x86_64.zip && \
#     unzip -d /opt/mono/windows-x86_64 windows-x86_64.zip && \
    unzip -d /opt/mono/wasm-runtime-threads wasm-runtime-threads.zip && \
    unzip -d mono-bcl/bcl-desktop bcl-desktop.zip && \
    unzip -d mono-bcl/bcl-wasm bcl-wasm.zip && \
    mkdir -p /opt/mono/linux-x86_64/desktop-linux-x86_64-release/lib/mono && \
#    mkdir -p /opt/mono/windows-x86_64/desktop-windows-x86_64-release/lib/mono && \
    mkdir -p /opt/mono/wasm-runtime-threads/wasm-runtime-threads-release/lib/mono && \
    mv -f mono-bcl/bcl-desktop/desktop-bcl/net_4_x /opt/mono/linux-x86_64/desktop-linux-x86_64-release/lib/mono/4.5 && \
#    cp -rf /opt/mono/linux-x86_64/desktop-linux-x86_64-release/lib/mono/4.5 /opt/mono/windows-x86_64/desktop-windows-x86_64-release/lib/mono/ && \
    mv -f mono-bcl/bcl-wasm/wasm-bcl/wasm /opt/mono/wasm-runtime-threads/wasm-runtime-threads-release/lib/mono/wasm && \
    mv -f mono-bcl/bcl-wasm/wasm-bcl/wasm_tools /opt/mono/wasm-runtime-threads/wasm-runtime-threads-release/lib/mono/wasm_tools && \
    rm -f linux-x86_64.zip windows-x86_64.zip wasm-runtime-threads.zip bcl-desktop.zip bcl-wasm.zip && \
    rm -rf mono-bcl
#    ln -s /opt/mono/linux-x86_64/desktop-linux-x86_64-release/bin/mono /usr/bin/mono && \
#    mkdir -p /usr/lib/mono && ln -s /opt/mono/linux-x86_64/desktop-linux-x86_64-release/lib/mono/4.5 /usr/lib/mono/4.5

COPY mono-windows/ /opt/mono/windows-x86_64/desktop-windows-x86_64-release

RUN chmod -R 0777 /opt/mono

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
