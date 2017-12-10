project(writer)

# 编译器选项
if (WIN32)
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /MTd")
endif()

# 源文件
file(GLOB WRITER_SROUCE
  "./*.*")

# 公共源文件
aux_source_directory("../include" WRITER_SROUCE)

# 公共头文件
include_directories(${PROJECT_SOURCE_DIR}/../include)

# protobuf依赖
include_directories("E:\\3rd\\protobuf\\include")
link_directories("E:\\3rd\\protobuf\\lib")


add_executable(${PROJECT_NAME} ${WRITER_SROUCE})

# 加入protobuf依赖模块
target_link_libraries(${PROJECT_NAME} libprotobufd.lib)


# Creates a folder "executables" and adds target 
# Properties->General->Output Directory
set_target_properties(${PROJECT_NAME} PROPERTIES
  FOLDER "executables"
  RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/bin)