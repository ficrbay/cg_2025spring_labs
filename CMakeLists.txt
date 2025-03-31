cmake_minimum_required(VERSION 3.10)
project(OpenGL_Project)

set(CMAKE_CXX_STANDARD 17)

# 自动通过 vcpkg 查找依赖
find_package(OpenGL REQUIRED)
find_package(glfw3 REQUIRED)
find_package(freetype REQUIRED)

# 包含头文件路径
include_directories(${PROJECT_SOURCE_DIR}/include)

# 搜集源文件，包括 glad.c
file(GLOB SOURCES
    ${PROJECT_SOURCE_DIR}/src/*.cpp
    ${PROJECT_SOURCE_DIR}/src/glad.c
)

# 创建可执行文件
add_executable(OpenGL_Project ${SOURCES})

# 拷贝资源目录（shaders, fonts, static/images）
foreach(dir shaders fonts)
    if(EXISTS ${PROJECT_SOURCE_DIR}/${dir})
        add_custom_command(TARGET OpenGL_Project POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy_directory
            ${PROJECT_SOURCE_DIR}/${dir}
            $<TARGET_FILE_DIR:OpenGL_Project>/${dir}
        )
    endif()
endforeach()

# 单独处理 static/images（你实际的纹理图片目录）
if(EXISTS ${PROJECT_SOURCE_DIR}/static/images)
    add_custom_command(TARGET OpenGL_Project POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E make_directory
        $<TARGET_FILE_DIR:OpenGL_Project>/static
    )
    add_custom_command(TARGET OpenGL_Project POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_directory
        ${PROJECT_SOURCE_DIR}/static/images
        $<TARGET_FILE_DIR:OpenGL_Project>/static/images
    )
endif()

# 链接库（无 GLEW）
target_link_libraries(OpenGL_Project
    OpenGL::GL
    glfw
    freetype
)

# 可选：输出构建完成提示
add_custom_command(TARGET OpenGL_Project POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E echo "✅ Build complete. Executable and resources are in $<TARGET_FILE_DIR:OpenGL_Project>"
)
