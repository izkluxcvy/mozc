# How to build Mozc for OpenBSD

## Requirements
git, cmake, python-3.x, (gmake, fcitx5, ibus, qt6, ..)

## Build
```sh
git clone https://github.com/izkluxcvy/mozc-ports.git
cd mozc-ports
cmake -S cmake -B build -G "Unix Makefiles" \
    -DCMAKE_MAKE_PROGRAM=gmake \
    -DBUILD_TOOL=ON -DBUILD_FCITX=ON -DBUILD_IBUS=ON -DCMAKE_BUILD_TYPE=Release
cmake --build build
doas cmake --install build
```
