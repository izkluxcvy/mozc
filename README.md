## Mozc ports
Switch build system to CMake and add OpenBSD flag.

### Copied files
- [google/mozc](https://github.com/google/mozc)
    - /, upstream.
- [fcitx/mozc](https://github.com/fcitx/mozc)
    - /src/unix/fcitx5, upstream for fcitx5.
- [fcitx/mozc-cmake](https://github.com/fcitx/mozc-cmake)
    - /cmake, cmake base repo.

### Build
[docs/build_mozc_for_openbsd.md](docs/build_mozc_for_openbsd.md)

### Misc
It seems that upstream code has only been partially clang-formatted.
