FROM fedora:37

WORKDIR /root

RUN dnf -y install --setopt=install_weak_deps=False \
      bash bzip2 curl file findutils git make nano patch pkgconfig python3-pip unzip which xz && \
    pip install scons==4.3.0

ARG mono_version=mono-6.12.0.182

RUN files_root=$(pwd)/files && \
    mono_root="${files_root}/${mono_version}" && \
    if [ ! -e ${mono_root} ]; then \
        git clone -b ${mono_version} --single-branch --progress --depth 1 https://github.com/mono/mono ${mono_root} \
        pushd ${mono_root} \
        # Download all submodules, up to 6 at a time
        git submodule update --init --recursive --recommend-shallow -j 6 --progress \
        # Set up godot-mono-builds in tree
        git clone --progress https://github.com/godotengine/godot-mono-builds \
        pushd godot-mono-builds \
        git checkout 8767196960fb884ae95f337ee10cb131ae720086 \
        export MONO_SOURCE_ROOT=${mono_root} \
        python3 patch_mono.py \
        popd \
        popd \
fi

RUN if [ -z "${mono_version}" ]; then echo -e "\n\nargument mono-version is mandatory!\n\n"; exit 1; fi && \
    dnf -y install --setopt=install_weak_deps=False \
      autoconf automake cmake gcc gcc-c++ gettext libtool perl python-unversioned-command && \
    cp -a /root/files/${mono_version} /root && \
    cd /root/${mono_version} && \
    NOCONFIGURE=1 ./autogen.sh && \
    ./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var/lib/mono --disable-boehm --host=x86_64-linux-gnu && \
    make -j && \
    make install && \
    cd /root && \
    rm -rf /root/${mono_version} && \
    cert-sync /etc/pki/tls/certs/ca-bundle.crt && \
    rpm -ivh --nodeps \
      https://download.mono-project.com/repo/centos8-preview/m/msbuild/msbuild-16.10.1+xamarinxplat.2021.05.26.14.00-0.xamarin.7.epel8.noarch.rpm \
      https://download.mono-project.com/repo/centos8-preview/m/msbuild/msbuild-sdkresolver-16.10.1+xamarinxplat.2021.05.26.14.00-0.xamarin.7.epel8.noarch.rpm \
      https://download.mono-project.com/repo/centos8-preview/m/msbuild-libhostfxr/msbuild-libhostfxr-3.0.0.2019.04.16.02.13-0.xamarin.4.epel8.x86_64.rpm \
      https://download.mono-project.com/repo/centos8-preview/n/nuget/nuget-5.6.0.6489.bin-0.xamarin.1.epel8.noarch.rpm

RUN if [ -z "${mono_version}" ]; then echo -e "\n\nargument mono-version is mandatory!\n\n"; exit 1; fi && \
    dnf -y install --setopt=install_weak_deps=False \
      mingw32-gcc mingw32-gcc-c++ mingw32-winpthreads-static mingw64-gcc mingw64-gcc-c++ mingw64-winpthreads-static && \
    cp -a /root/files/${mono_version} /root && \
    export MONO_SOURCE_ROOT=/root/${mono_version} && \
    cd /root/${mono_version}/godot-mono-builds && \
    python3 windows.py configure -j --target=x86_64 --target=x86 --mxe-prefix=/usr && \
    python3 windows.py make -j --target=x86_64 --target=x86 --mxe-prefix=/usr && \
    # 'desktop' bcl needed to build 'desktop-win32', then we can remove it.
    python3 bcl.py make -j --product=desktop && \
    python3 bcl.py make -j --product=desktop-win32 && \
    rm -rf /root/mono-installs/desktop-bcl && \
    python3 windows.py copy-bcl --target=x86_64 --target=x86 && \
    cd /root && \
    rm -rf /root/${mono_version}

####
# Old
####

# FROM mono:6.12.0.182

# RUN apt update
# RUN apt install -y pkg-config mingw-w64 build-essential scons pkg-config libx11-dev libxcursor-dev libxinerama-dev libgl1-mesa-dev libglu-dev libasound2-dev libpulse-dev libudev-dev libxi-dev libxrandr-dev yasm

# COPY mono-windows/ /opt/mono-windows
# RUN chmod -R 0777 /opt/mono-windows
# # RUN chgrp -R 1000:1000 /opt/mono-windows

# USER root
# RUN update-alternatives --install /usr/bin/x86_64-w64-mingw32-gcc x86_64-w64-mingw32-gcc /usr/bin/x86_64-w64-mingw32-gcc-posix 90
# RUN update-alternatives --install /usr/bin/x86_64-w64-mingw32-g++ x86_64-w64-mingw32-g++ /usr/bin/x86_64-w64-mingw32-g++-posix 90

# RUN cp /usr/bin/ar bin/x86_64-w64-mingw32-gcc-ar
# RUN cp /usr/bin/ld bin/x86_64-w64-mingw32-gcc-ld
# RUN cp /usr/bin/ranlib bin/x86_64-w64-mingw32-gcc-ranlib

# RUN mkdir /.config
# RUN mkdir /.config/NuGet
# COPY NuGet.Config /.config/NuGet/NuGet.Config
# RUN chmod -R 0777 /.config

# RUN mkdir /.nuget
# RUN chmod -R 0777 /.nuget

# RUN mkdir /.local
# RUN chmod -R 0777 /.local

# RUN chmod -R 0777 /var/lib/alternatives
