# 这个CMake文件定义了一个名为minnow的项目，并指定了其构建过程。


# 指定了CMake所需的最小版本为3.24.2
cmake_minimum_required(VERSION 3.24.2)

# 定义了一个CMake项目，项目名称为minnow，并且指定了项目的主要编程语言为C++ (CXX)。
#   CXX 是CMake中用于表示C++语言的标准标识符。如果项目还涉及其他语言（如C、Fortran等），
#   可以在这里添加更多的语言标识符。
project(minnow CXX)

# 检查CMake是否在Unix系统上使用Makefiles作为生成器。如果是，进入下面的条件块  STREQUAL = String euqal
# 如果在Unix环境下，CMake将构建过程的make程序设置为make-parallel.sh脚本（位于项目源目录下的/scripts文件夹中）
if(${CMAKE_GENERATOR} STREQUAL "Unix Makefiles")
  set(CMAKE_MAKE_PROGRAM "${PROJECT_SOURCE_DIR}/scripts/make-parallel.sh" CACHE STRING "" FORCE)
endif()

# 检查源代码目录（PROJECT_SOURCE_DIR）是否与构建目录（CMAKE_BINARY_DIR）相同。如果是，进入下面的条件块
if(${PROJECT_SOURCE_DIR} STREQUAL ${CMAKE_BINARY_DIR})
# （例如，使用cmake -B build命令指定构建目录）
  message(FATAL_ERROR "Minnow must be built outside its source directory, e.g. `cmake -B build`.")
endif()

# 包含etc/build_type.cmake文件，可能定义了与构建类型（如Release、Debug）相关的设置。
include(etc/build_type.cmake)
# 包含etc/cflags.cmake文件，这个文件可能设置了编译器的标志（如优化级别、警告设置等）。
include(etc/cflags.cmake)
# 包含etc/scanners.cmake文件，可能定义了扫描工具相关的设置。
include(etc/scanners.cmake)
# 包含etc/tests.cmake文件，可能包含了测试相关的配置或设置。
include(etc/tests.cmake)


# 添加"${PROJECT_SOURCE_DIR}/util"目录到项目的包含路径中。这样，项目中的源文件就可以引用util目录下的头文件。
include_directories("${PROJECT_SOURCE_DIR}/util")
# 同上
include_directories("${PROJECT_SOURCE_DIR}/src")


# 42- 50行添加了多个子目录用于构建不同的项目模块（util、src、tests、apps）。

# 将util目录添加为子目录。这意味着CMake会进入util目录并执行该目录下的CMakeLists.txt文件。
# 通常，这样做是为了构建util目录中的库或工具，并将其集成到主项目中。
add_subdirectory("${PROJECT_SOURCE_DIR}/util")
# 类似地，将src目录添加为子目录。CMake将进入src目录并构建其中的内容。
add_subdirectory("${PROJECT_SOURCE_DIR}/src")
# 将tests目录添加为子目录，CMake将构建测试文件，通常是为了编译和运行单元测试或集成测试。
add_subdirectory("${PROJECT_SOURCE_DIR}/tests")
# 将apps目录添加为子目录，CMake将构建apps目录中的应用程序部分，可能是项目的一些可执行文件或示例。
add_subdirectory("${PROJECT_SOURCE_DIR}/apps")


# 启用生成compile_commands.json文件。这个文件包含了CMake生成的编译命令，可以供IDE（如VSCode）
# 或其他工具（如clang-tidy）使用，提供更好的代码分析支持。
# (该文件是一个符号链接 compile_commands.json -> build/compile_commands.json)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)