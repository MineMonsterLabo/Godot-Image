FROM mono:6.12.0.182

RUN apt update
RUN apt install -y pkg-config mingw-w64 build-essential scons pkg-config libx11-dev libxcursor-dev libxinerama-dev libgl1-mesa-dev libglu-dev libasound2-dev libpulse-dev libudev-dev libxi-dev libxrandr-dev yasm

COPY mono-windows/ /opt/mono-windows
RUN chmod -R 0777 /opt/mono-windows
# RUN chgrp -R 1000:1000 /opt/mono-windows
