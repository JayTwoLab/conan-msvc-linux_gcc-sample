cmake_minimum_required(VERSION 3.21)
project(conan_sample LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(app src/main.cpp)

find_package(Boost CONFIG REQUIRED)

if(TARGET Boost::headers)
  target_link_libraries(app PRIVATE Boost::headers)
elseif(TARGET Boost::boost)
  target_link_libraries(app PRIVATE Boost::boost)
else()
  message(FATAL_ERROR "Boost target not found (Boost::headers / Boost::boost).")
endif()
