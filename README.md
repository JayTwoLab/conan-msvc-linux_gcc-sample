
# 코난 프로젝트 빌드 명령 

## 사전 설치 항목
- `Windows` : **Visual Studio** <sub> (2022 or higher) </sub>, **cmake**, **ninja**
- `Linux` : **gcc** <sub> (g++) </sub>, **cmake**, **ninja**

<br />

## 프로젝트 설정
- `conanfile.txt` 파일 생성
```ini
[requires]
boost/1.84.0

[options]
boost/*:shared=False

[generators]
CMakeToolchain
CMakeDeps
```

- 위의 경우 boost 버전 1.84.0 을 사용

<br />

## `Visual Studio 2022`

- 코난 프로필 파일을 사전에 생성한다.

<br />

- 릴리즈 빌드를 위한 설정 파일 생성: `C:\Users\<user>\.conan2\profiles\msvc_release` (`<user>`는 개인 계정)
```ini
[settings]
os=Windows
arch=x86_64
build_type=Release
compiler=msvc
compiler.version=194
compiler.runtime=dynamic
compiler.runtime_type=Release
compiler.cppstd=17

[conf]
tools.cmake.cmaketoolchain:generator=Ninja
```
- `compiler.version`
   - Visual Studio 버전+ 제품 버전(MSVC Toolset), `Conan compiler.version`
      - `Visual Studio 2026`, `195`
      - `Visual Studio 2022 17.10~`, `194`
      - `Visual Studio 2022 17.0~17.9`, `193`
      - `Visual Studio 2019`, `192`
      - `Visual Studio 2017`, `191`
      - `Visual Studio 2015`, `190`
      - `cl` 명령을 입력하면 표시되는 버전의 상위 3자리 값: 예> 19.50.35725 이면 195 를 사용

<br />

- 디버깅 빌드를 위한 설정 파일 생성: `C:\Users\<user>\.conan2\profiles\msvc_debug`
```ini
[settings]
os=Windows
arch=x86_64
build_type=Debug
compiler=msvc
compiler.version=194
compiler.runtime=dynamic
compiler.runtime_type=Debug
compiler.cppstd=17

[conf]
tools.cmake.cmaketoolchain:generator=Ninja
```

<br />

---

- `Visual Studio`인 경우 `Release` 빌드 절차
```sh
# msvc cmd 에서 다음과 같은 명령들을 실행한다. 

cmake -E rm -rf build-msvc-release
# build-msvc 디렉터리를 존재 여부와 관계없이, 하위 내용까지 전부 삭제

conan install . ^
 -pr:h %USERPROFILE%\.conan2\profiles\msvc_release ^
 -pr:b default ^
 -of build-msvc-release ^
 --build=missing
# conan install . : 현재 디렉터리(.)에 있는 conanfile.txt 또는 conanfile.py를 기준
# -pr:h %USERPROFILE%\.conan2\profiles\msvc_release : host profile 지정
# -pr:b default : 보통 host와 동일하면 default를 사용
# -of build-msvc-release : Conan이 생성하는 파일들을 build-msvc-release 디렉터리에 출력  
# --build=missing : 로컬 캐시에 없는 패키지는 소스에서 빌드. 이미 캐시에 있으면 빌드하지 않음. 

cmake -S . ^
 -B build-msvc-release ^
 -DCMAKE_TOOLCHAIN_FILE=build-msvc\conan_toolchain.cmake ^
 -G "Visual Studio 17 2022"
# -S . : 현재 디렉터리(.)에 CMakeLists.txt가 있음
# -B build-msvc : CMake 결과물을 build-msvc에 생성
# -DCMAKE_TOOLCHAIN_FILE=build-msvc\conan_toolchain.cmake : 

cmake --build build-msvc-release --config Release
# --build build-msvc-release : build-msvc-release 경로 빌드
# --config Release : 프로필 파일(msvc_release)에 Release로 설정된 경우, 릴리즈로 빌드하여야 함.
```

<br />

- `Visual Studio`인 경우 `Debug` 빌드 절차
```sh
cmake -E rm -rf build-msvc-debug

conan install . ^
  -pr:h %USERPROFILE%\.conan2\profiles\msvc_debug ^
  -pr:b default ^
  -of build-msvc-debug ^
  --build=missing

cmake -S . -B build-msvc-debug ^
  -DCMAKE_TOOLCHAIN_FILE=build-msvc-debug\conan_toolchain.cmake ^
  -G "Visual Studio 17 2022"

cmake --build build-msvc-debug --config Debug
```

<br />

---

## `Rocky Linux 8 x86_64`

- 릴리즈 빌드를 위한 설정 파일 생성: `/home/<user>/.conan2/profiles/linux_gcc_release`
```ini
[settings]
os=Linux
arch=x86_64
compiler=gcc
compiler.version=8
compiler.libcxx=libstdc++11
build_type=Release

[conf]
tools.cmake.cmaketoolchain:generator=Ninja
```

<br />

- Release 빌드
```sh
cmake -E rm -rf build-linux-gcc-release

conan install . \
 -pr:h ~/.conan2/profiles/linux_gcc_release \
 -pr:b default \
 -of build-linux-gcc-release \
 --build=b2* \
 --build=missing
# b2의 종속성 문제가 있을 경우를 대비하여 b2도 빌드함

cmake -S . \
 -B build-linux-gcc-release \
 -DCMAKE_TOOLCHAIN_FILE=build-linux-gcc/conan_toolchain.cmake \
 -G "Unix Makefiles" \
 -DCMAKE_BUILD_TYPE=Release

cmake --build build-linux-gcc-release --config Release
```







