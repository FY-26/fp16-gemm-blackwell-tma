cmake_minimum_required(VERSION 3.18 FATAL_ERROR) # 支持 CUDA 的最低推荐版本
project(CuTe_GEMM_Project LANGUAGES CXX CUDA)

# 1. 强制使用 C++17 (CUTLASS 3.x 和 CuTe 的硬性要求)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CUDA_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CUDA_STANDARD_REQUIRED ON)

# 2. 极其方便的架构切换配置！
# 这里填入你想支持的架构，80代表Ampere(A100/30系)，89代表Ada(40系)，90代表Hopper(H100)
# 你甚至可以写多个，编译出一个能在这几张卡上都能跑的胖二进制 (Fat-binary)
set(CMAKE_CUDA_ARCHITECTURES "120a") 

# 3. 设置 NVCC 特有编译优化参数
set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} -O3 --expt-relaxed-constexpr -lineinfo")

# 4. 找到系统的 CUDA Toolkit
find_package(CUDAToolkit REQUIRED)

# 5. 定义你的可执行文件 (把你之前的 cu 文件名填进来)
add_executable(gemm_test fp16_gemm_blackwell_sm120a_tma.cu)

# 6. 配置 CUTLASS 头文件路径 (极度重要)
# 假设你把 cutlass 源码克隆在了当前目录的 cutlass 文件夹下
set(CUTLASS_DIR "./cutlass")

target_include_directories(gemm_test PRIVATE
    ${CUTLASS_DIR}/include
    ${CUTLASS_DIR}/tools/util/include
)

# 7. 链接 CUDA 运行时库
target_link_libraries(gemm_test PRIVATE CUDA::cudart CUDA::cublas)