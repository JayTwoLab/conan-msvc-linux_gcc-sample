
- 코난 프로젝트 빌드 명령 (MSVC 2022)

```
# msvc cmd 에서 다음과 같은 명령들을 실행한다. 

cmake -E rm -rf build-msvc
# build-msvc 디렉터리를 존재 여부와 관계없이, 하위 내용까지 전부 삭제

conan install .   -pr:h %USERPROFILE%\.conan2\profiles\msvc_release   -pr:b default   -of build-msvc   --build=missing
# conan install . : 현재 디렉터리(.)에 있는 conanfile.txt 또는 conanfile.py를 기준
# -pr:h %USERPROFILE%\.conan2\profiles\msvc_release : host profile 지정
# -pr:b default : 보통 host와 동일하면 default를 사용
# -of build-msvc : Conan이 생성하는 파일들을 build-msvc 디렉터리에 출력  
# --build=missing : 로컬 캐시에 없는 패키지는 소스에서 빌드. 이미 캐시에 있으면 빌드하지 않음. 

cmake -S . -B build-msvc   -DCMAKE_TOOLCHAIN_FILE=build-msvc\conan_toolchain.cmake   -G "Visual Studio 17 2022"
# -S . : 현재 디렉터리(.)에 CMakeLists.txt가 있음
# -B build-msvc : CMake 결과물을 build-msvc에 생성
# -DCMAKE_TOOLCHAIN_FILE=build-msvc\conan_toolchain.cmake : 

cmake --build build-msvc --config Release
# --build build-msvc : build-msvc 경로의 것을 빌드
# --config Release : 릴리즈로 빌드
```





