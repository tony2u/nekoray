git clone https://github.com/tony2u/nekoray.git --recursive

Windows:
using Visual Studio
1. Preparation (only first time)
   conan profile detect --force
   copy C:\Users\tony2u\.conan2\profiles\default C:\Users\tony2u\.conan2\profiles\Windows.Release (modify compiler.cppstd in Windows.Release： compiler.cppstd=14 ===> compiler.cppstd=17, compiler=msvc)
2. Build dependencies
   conan install . -b missing -pr Windows.Release
3. Build nekoray
   cd build
   cmake .. -G "Visual Studio 17 2022" -DCMAKE_TOOLCHAIN_FILE=.\generators\conan_toolchain.cmake -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_POLICY_DEFAULT_CMP0057=NEW -DQT_VERSION_MAJOR=6
   cmake --build . --config Release

Linux:
1. Preparation (only first time)
   conan profile detect --force
   cp ~/.conan2/profiles/default ~/.conan2/profiles/Linux.Release (confirm compiler.cppstd in Linux.Release： compiler.cppstd=gnu17 or compiler.cppstd=17)
2. Build dependencies
   export NOT_ON_C3I=1 && conan install . -b missing -pr Linux.Release
   (qt/6.5.3: Invalid: qt is not supported on gcc11 and clang >= 12 on C3I until conan-io/conan-center-index#13472 is fixed)
3. Build nekoray
   cd build
   cmake .. -DCMAKE_TOOLCHAIN_FILE=./Release/generators/conan_toolchain.cmake -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_POLICY_DEFAULT_CMP0057=NEW -DCMAKE_BUILD_TYPE=Release -DQT_VERSION_MAJOR=6
   cmake --build .

macOS:
1. Preparation (only first time)
   conan profile detect --force (only first time)
   cp ~/.conan2/profiles/default ~/.conan2/profiles/Macos.Release (confirm compiler.cppstd in Macos.Release： compiler.cppstd=gnu17 or compiler.cppstd=17)
2. Build dependencies
   conan install conanfile_qt5.txt -b missing -pr Macos.Release  #QT 5.15 on macOS 10.12 or later
   or
   conan install conanfile_qt64.txt -b missing -pr Macos.Release #QT 6.4 on macOS 10.14 or later
   or
   conan install . -b missing -pr Macos.Release                  #QT 6.5, only macOS 11.0 or later, because cmake_automoc_parser only run on 11.0.0 or later
3. Build nekoray
   cd build
   cmake .. -DCMAKE_TOOLCHAIN_FILE=./Release/generators/conan_toolchain.cmake -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_POLICY_DEFAULT_CMP0057=NEW -DCMAKE_BUILD_TYPE=Release -DQT_VERSION_MAJOR=5 #QT 5.15
   or
   cmake .. -DCMAKE_TOOLCHAIN_FILE=./Release/generators/conan_toolchain.cmake -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_POLICY_DEFAULT_CMP0057=NEW -DCMAKE_BUILD_TYPE=Release -DQT_VERSION_MAJOR=6 #QT 6.x
   cmake --build .

FreeBSD:
conanfile.txt
[requires]
protobuf/3.21.4
yaml-cpp/0.7.0
zxing-cpp/2.0.0

[generators]
CMakeDeps
CMakeToolchain

[layout]
cmake_layout

patching to conan python file:
~/.local/lib/python3.9/site-packages/conan/tools/gnu/autotools.py
def make(self, target=None, args=None):
...
+if jobs=="-j1":
+    jobs = ""

1. Preparation (only first time)
   conan profile detect --force (only first time)
   cp ~/.conan2/profiles/default ~/.conan2/profiles/FreeBSD.Release (confirm compiler.cppstd in FreeBSD.Release： compiler.cppstd=gnu17 or compiler.cppstd=17)
   append two lines to ~/.conan2/global.conf:
   tools.system.package_manager:mode = install
   tools.system.package_manager:sudo = True
2. Build dependencies
    setenv NOT_ON_C3I 1
    conan install conanfile_qt5.txt -b missing -pr FreeBSD.Release  #QT 5.15 on FreeBSD, qt5, qt5-linguist, qt5-svg, qt5-x11extras
    or
    conan install . -b missing -pr FreeBSD.Release -c tools.build:jobs=1 #QT 6.5 on FreeBSD, qt6-base, qt6-svg, qt6-tools
3. Build nekoray
   cd build
   cmake .. -DCMAKE_TOOLCHAIN_FILE=./Release/generators/conan_toolchain.cmake -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_POLICY_DEFAULT_CMP0057=NEW -DCMAKE_BUILD_TYPE=Release -DQT_VERSION_MAJOR=6
   cmake --build .
