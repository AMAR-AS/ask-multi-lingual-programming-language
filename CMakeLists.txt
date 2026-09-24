cmake_minimum_required(VERSION 3.16)

project(Ask
    VERSION 0.1.0
    DESCRIPTION "Ask — a multilingual, Unicode-first programming language"
    LANGUAGES CXX
)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

include_directories(include)

file(GLOB ASK_SOURCES CONFIGURE_DEPENDS
    "src/*.cpp"
)

add_executable(ask ${ASK_SOURCES})

target_compile_features(ask PRIVATE cxx_std_17)

if (MSVC)
    target_compile_options(ask PRIVATE /W4)
else()
    target_compile_options(ask PRIVATE -Wall -Wextra -Wpedantic)
endif()
