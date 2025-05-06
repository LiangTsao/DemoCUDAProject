cmake_minimum_required(VERSION 3.20)


project(demo LANGUAGES C CXX CUDA)

find_package(CUDAToolkit REQUIRED)

set(CMAKE_POSITION_INDEPENDENT_CODE ON)

set(CMAKE_CUDA_ARCHITECTURES 50 52 60 61 70 72 75 80 86 87 89 90 100 101 120)
set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} -Wno-deprecated-gpu-targets")
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    # set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} -G")  # enable cuda-gdb (expensive)
endif()


# 设置CUDA相关变量
set(CUDA_PATH $ENV{CUDA_PATH})

# 检查CUDA_PATH是否设置
if(NOT CUDA_PATH)
    message(FATAL_ERROR "CUDA_PATH environment variable is not set.")
endif()

# 设置CUDA工具链路径
set(CMAKE_CUDA_COMPILER "${CUDA_PATH}/bin/nvcc")

# 设置CUDA相关的头文件和库文件路径
include_directories("${CUDA_PATH}/include")
include_directories(Common)
link_directories("${CUDA_PATH}/lib/x64") # 根据实际情况修改库文件路径


# 设置Windows SDK路径  
#    系统 INCLUDE LIB 环境变量
# include_directories(C:/Program Files (x86)/Windows Kits/10/Include/10.0.22621.0/ucrt)
# include_directories(C:/Program Files (x86)/Windows Kits/10/Include/10.0.22621.0/um)
# include_directories(C:/Program Files (x86)/Windows Kits/10/Include/10.0.22621.0/shared)
# link_directories("C:/Program Files (x86)/Windows Kits/10/Lib/10.0.22621.0/ucrt/x64")
# link_directories("C:/Program Files (x86)/Windows Kits/10/Lib/10.0.22621.0/um/x64")

# 设置项目支持CUDA语言
#enable_language(CUDA)

# Source file
# Add target for bandwidthTest
add_executable(bandwidthTest src/bandwidthTest.cu)

target_compile_options(bandwidthTest PRIVATE $<$<COMPILE_LANGUAGE:CUDA>:--extended-lambda>)

target_compile_features(bandwidthTest PRIVATE cxx_std_17 cuda_std_17)

set_target_properties(bandwidthTest PROPERTIES CUDA_SEPARABLE_COMPILATION ON)

# 链接CUDA库
target_link_libraries(bandwidthTest cudart)