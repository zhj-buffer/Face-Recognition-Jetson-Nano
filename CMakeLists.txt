cmake_minimum_required(VERSION 3.5)
project(main)
SET(CUDA_VERSION 10)
FIND_PACKAGE(CUDA ${CUDA_VERSION} REQUIRED)

add_definitions(-std=c++11)
#set(QMAKE_CXXFLAGS "-std=c++11")
include_directories(/usr/local/cuda/include)
#include_directories(/home/zhou/tensorrt/include)
link_directories(/usr/local/cuda /usr/include /usr/local/lib/ncnn /usr/local/lib)
include_directories( /usr/local/include ./include /usr/local/include/ncnn /usr/local/include/opencv4)

#find_package( OpenCV REQUIRED)
#include_directories(${OpenCV_INCLUDE_DIRS})

#find_package( dlib REQUIRED)
#find_package ( Boost REQUIRED COMPONENTS system filesystem python)

include_directories(${Boost_INCLUDE_DIRS})

AUX_SOURCE_DIRECTORY(./src DIR_SRCS)
#cuda_add_executable( main ${DIR_SRCS} src/pnet_rt.cpp src/common.h src/common.cpp src/pnet_rt.h src/rnet_rt.cpp src/rnet_rt.h src/baseEngine.cpp src/baseEngine.h src/onet_rt.h src/onet_rt.cpp src/kernels.h src/kernels.cu src/face_embedder.h src/face_embedder.cpp src/alignment.h src/alignment.cpp src/face_classifier.h src/face_classifier.cpp src/test.h src/test.cpp)
#cuda_add_executable( main ${DIR_SRCS} src/pnet_rt.cpp src/common.h src/common.cpp src/pnet_rt.h src/rnet_rt.cpp src/rnet_rt.h src/baseEngine.cpp src/baseEngine.h src/onet_rt.h src/onet_rt.cpp src/kernels.h  src/face_embedder.h src/face_embedder.cpp src/alignment.h src/alignment.cpp src/face_classifier.h src/face_classifier.cpp src/test.h src/test.cpp)


set(CMAKE_CXX_FLAGS  "-lpthread")
set(CMAKE_CXX_FLAGS  "-O3 -fopenmp -ldl -lpthread -pthread -lgomp -DNDEBUG -rdynamic")



#Use Boost
find_package(Boost COMPONENTS system filesystem REQUIRED)
include_directories(${Boost_INCLUDE_DIR})
link_directories(${Boost_LIBRARY_DIR})


# Use OpenMP
    if (USE_OPENMP)
    find_package(OpenMP QUIET)
if (OPENMP_FOUND)
    message(STATUS "Use OpenMP")
add_definitions(-DUSE_OPENMP)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")
    endif()
endif()

#Use OpenCV
#find_package(OpenCV)
#include_directories(${OpenCV_INCLUDE_DIRS})


AUX_SOURCE_DIRECTORY(./src DIR_SRCS)

#target_link_libraries( main ${Boost_LIBRARIES})
#target_link_libraries( main ${OpenCV_LIBS} )
#target_link_libraries( main ncnn )
#target_link_libraries( main ncnn dl pthread gomp)  

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)
#add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/src)


# 指定编译库输出的位置，这个可以设置也可以不设置
#set(LIBRARY_OUTPUT_PATH lib)
# 生成动态库
#add_library(mylib SHARED ${DIR_LIB_SRCS})

 # 生成静态库
# add_library(mylib_static STATIC  ${DIR_LIB_SRCS})
# 想要生成动态库和静态库相同名称的的库文件，则需要一个指令set_target_properties
# 这里只是通过重新命名的方法使其名字相同的，构建时还需不能相同
#set_target_properties(mylib_static PROPERTIES OUTPUT_NAME "mylib")
# 将动态库和静态库安装到<prefix>/lib
#install(TARGETS mylib mylib_static LIBRARY DESTINATION lib ARCHIVE DESTINATION lib)
# 安装头文件
#install(FILES func.h DESTINATION include)

#add_subdirectory(src)

file(GLOB_RECURSE FACE_LIB_SRC
            src//*.cpp
)

file(GLOB_RECURSE FACE_LIB_HEADERS include/*.h)
install(FILES ${FACE_LIB_HEADERS} DESTINATION include/)

add_library(face STATIC ${FACE_LIB_SRC})


# Build examples
message(STATUS "Build with examples.")
#list(APPEND ncnn pthread gomp dl glog ${OpenCV_LIBS} boost_filesystem)
add_executable(facerec
        src/main.cpp )
#target_link_libraries(facerec face ncnn pthread gomp dl glog boost_filesystem vulkan ${OpenCV_LIBS})
target_link_libraries(facerec face ncnn glslang SPIRV MachineIndependent OGLCompiler  OSDependent  GenericCodeGen  pthread gomp dl glog boost_filesystem vulkan opencv_highgui opencv_imgcodecs opencv_imgproc  opencv_videoio opencv_core )


install(TARGETS face facerec
        RUNTIME DESTINATION bin
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib)



