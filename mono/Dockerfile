FROM fedora:37

WORKDIR /root

RUN dnf -y install --setopt=install_weak_deps=False \
      bash bzip2 curl file findutils git make nano patch pkgconfig python3-pip unzip which xz && \
    pip install scons==4.3.0

RUN rpm --import "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF" && \
    su -c 'curl https://download.mono-project.com/repo/centos8-stable.repo | tee /etc/yum.repos.d/mono-centos8-stable.repo' && \
    dnf -y update && \
    dnf -y install mono-devel

ARG mono_version=mono-6.12.0.182

RUN if [ -z "${mono_version}" ]; then echo -e "\n\nargument mono-version is mandatory!\n\n"; exit 1; fi && \
    dnf -y install --setopt=install_weak_deps=False \
      autoconf automake cmake gcc gcc-c++ gettext libtool perl python-unversioned-command && \
    cert-sync /etc/pki/tls/certs/ca-bundle.crt && \
    rpm -ivh --nodeps \
      https://download.mono-project.com/repo/centos8-preview/m/msbuild/msbuild-16.10.1+xamarinxplat.2021.05.26.14.00-0.xamarin.7.epel8.noarch.rpm \
      https://download.mono-project.com/repo/centos8-preview/m/msbuild/msbuild-sdkresolver-16.10.1+xamarinxplat.2021.05.26.14.00-0.xamarin.7.epel8.noarch.rpm \
      https://download.mono-project.com/repo/centos8-preview/m/msbuild-libhostfxr/msbuild-libhostfxr-3.0.0.2019.04.16.02.13-0.xamarin.4.epel8.x86_64.rpm \
      https://download.mono-project.com/repo/centos8-preview/n/nuget/nuget-5.6.0.6489.bin-0.xamarin.1.epel8.noarch.rpm

RUN if [ -z "${mono_version}" ]; then echo -e "\n\nargument mono-version is mandatory!\n\n"; exit 1; fi && \
    dnf -y install --setopt=install_weak_deps=False \
      mingw32-gcc mingw32-gcc-c++ mingw32-winpthreads-static mingw64-gcc mingw64-gcc-c++ mingw64-winpthreads-static

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
