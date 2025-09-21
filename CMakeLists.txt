cmake_minimum_required(VERSION 3.16)
project(LearnOpenGL VERSION 1.0)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Set output directories
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# Include FetchContent module
include(FetchContent)

# Find OpenGL
find_package(OpenGL REQUIRED)

# Download and build GLFW
message(STATUS "Fetching GLFW...")
FetchContent_Declare(
    glfw
    GIT_REPOSITORY https://github.com/glfw/glfw.git
    GIT_TAG 3.3.8
)
set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
FetchContent_MakeAvailable(glfw)

# Download GLM (header-only)
message(STATUS "Fetching GLM...")
FetchContent_Declare(
    glm
    GIT_REPOSITORY https://github.com/g-truc/glm.git
    GIT_TAG 0.9.9.8
)
FetchContent_MakeAvailable(glm)

# Download Assimp
message(STATUS "Fetching Assimp...")
FetchContent_Declare(
    assimp
    GIT_REPOSITORY https://github.com/assimp/assimp.git
    GIT_TAG v5.2.5
)
set(ASSIMP_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(ASSIMP_BUILD_ASSIMP_TOOLS OFF CACHE BOOL "" FORCE)
set(ASSIMP_INSTALL OFF CACHE BOOL "" FORCE)
set(ASSIMP_WARNINGS_AS_ERRORS OFF CACHE BOOL "" FORCE)
FetchContent_MakeAvailable(assimp)

# Download STB (single header libraries)
message(STATUS "Fetching STB...")
FetchContent_Declare(
    stb
    GIT_REPOSITORY https://github.com/nothings/stb.git
    GIT_TAG master
)
FetchContent_MakeAvailable(stb)

# Create GLAD library (classic version for LearnOpenGL compatibility)
set(GLAD_DIR ${CMAKE_CURRENT_BINARY_DIR}/glad)
file(MAKE_DIRECTORY ${GLAD_DIR}/include/glad)
file(MAKE_DIRECTORY ${GLAD_DIR}/include/KHR)
file(MAKE_DIRECTORY ${GLAD_DIR}/src)

# Download classic GLAD files (OpenGL 3.3 Core)
file(DOWNLOAD 
    "https://raw.githubusercontent.com/Dav1dde/glad/c/include/glad/glad.h"
    "${GLAD_DIR}/include/glad/glad.h"
)
file(DOWNLOAD 
    "https://raw.githubusercontent.com/Dav1dde/glad/c/include/KHR/khrplatform.h"
    "${GLAD_DIR}/include/KHR/khrplatform.h"
)
file(DOWNLOAD 
    "https://raw.githubusercontent.com/Dav1dde/glad/c/src/glad.c"
    "${GLAD_DIR}/src/glad.c"
)

# Create GLAD library
add_library(glad ${GLAD_DIR}/src/glad.c)
target_include_directories(glad PUBLIC ${GLAD_DIR}/include)

# Create include directories list
set(INCLUDES
    ${GLAD_DIR}/include
    ${glm_SOURCE_DIR}
    ${stb_SOURCE_DIR}
    ${CMAKE_SOURCE_DIR}/src/common
)

# Create libraries list
set(LIBS
    glfw
    glad
    assimp
    OpenGL::GL
)

# Function to create tutorial executable
function(create_tutorial TUTORIAL_PATH TUTORIAL_NAME)
    add_executable(${TUTORIAL_NAME} ${TUTORIAL_PATH}/${TUTORIAL_NAME}.cpp)
    target_include_directories(${TUTORIAL_NAME} PRIVATE ${INCLUDES})
    target_link_libraries(${TUTORIAL_NAME} ${LIBS})
    
    # Copy resources to build directory
    add_custom_command(TARGET ${TUTORIAL_NAME} POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_directory
        ${CMAKE_SOURCE_DIR}/resources $<TARGET_FILE_DIR:${TUTORIAL_NAME}>/resources)
endfunction()

# Getting Started Tutorials
create_tutorial("src/getting_started/hello_window" "hello_window")
# create_tutorial("src/getting_started/hello_triangle" "hello_triangle")
# create_tutorial("src/getting_started/shaders" "shaders")
# create_tutorial("src/getting_started/textures" "textures")
# create_tutorial("src/getting_started/transformations" "transformations")
# create_tutorial("src/getting_started/coordinate_systems" "coordinate_systems")
# create_tutorial("src/getting_started/camera" "camera")

# Lighting Tutorials
# create_tutorial("src/2.lighting/2.1.colors" "colors")
# create_tutorial("src/2.lighting/2.2.basic_lighting" "basic_lighting")
# create_tutorial("src/2.lighting/2.3.materials" "materials")
# create_tutorial("src/2.lighting/2.4.lighting_maps" "lighting_maps")
# create_tutorial("src/2.lighting/2.5.light_casters" "light_casters")
# create_tutorial("src/2.lighting/2.6.multiple_lights" "multiple_lights")

# Model Loading Tutorials
# create_tutorial("src/3.model_loading/3.1.model_loading" "model_loading")

# Advanced OpenGL Tutorials
# create_tutorial("src/4.advanced_opengl/4.1.depth_testing" "depth_testing")
# create_tutorial("src/4.advanced_opengl/4.2.stencil_testing" "stencil_testing")
# create_tutorial("src/4.advanced_opengl/4.3.blending" "blending")
# create_tutorial("src/4.advanced_opengl/4.4.face_culling" "face_culling")
# create_tutorial("src/4.advanced_opengl/4.5.framebuffers" "framebuffers")
# create_tutorial("src/4.advanced_opengl/4.6.cubemaps" "cubemaps")
# create_tutorial("src/4.advanced_opengl/4.7.geometry_shader" "geometry_shader")
# create_tutorial("src/4.advanced_opengl/4.8.instancing" "instancing")

# Advanced Lighting Tutorials
# create_tutorial("src/5.advanced_lighting/5.1.blinn_phong" "blinn_phong")
# create_tutorial("src/5.advanced_lighting/5.2.gamma_correction" "gamma_correction")
# create_tutorial("src/5.advanced_lighting/5.3.shadow_mapping" "shadow_mapping")
# create_tutorial("src/5.advanced_lighting/5.4.point_shadows" "point_shadows")
# create_tutorial("src/5.advanced_lighting/5.5.normal_mapping" "normal_mapping")
# create_tutorial("src/5.advanced_lighting/5.6.parallax_mapping" "parallax_mapping")
# create_tutorial("src/5.advanced_lighting/5.7.hdr" "hdr")
# create_tutorial("src/5.advanced_lighting/5.8.bloom" "bloom")

# PBR Tutorials
# create_tutorial("src/6.pbr/6.1.lighting_theory" "pbr_theory")
# create_tutorial("src/6.pbr/6.2.lighting_textured" "pbr_textured")

# Set folders for Visual Studio organization
set_property(TARGET hello_window 
             PROPERTY FOLDER "1. Getting Started")
# set_property(TARGET hello_window hello_triangle shaders textures transformations coordinate_systems camera 
#              PROPERTY FOLDER "1. Getting Started")
# set_property(TARGET colors basic_lighting materials lighting_maps light_casters multiple_lights 
#              PROPERTY FOLDER "2. Lighting")
# set_property(TARGET model_loading 
#              PROPERTY FOLDER "3. Model Loading")
# set_property(TARGET depth_testing stencil_testing blending face_culling framebuffers cubemaps geometry_shader instancing 
#              PROPERTY FOLDER "4. Advanced OpenGL")
# set_property(TARGET blinn_phong gamma_correction shadow_mapping point_shadows normal_mapping parallax_mapping hdr bloom 
#              PROPERTY FOLDER "5. Advanced Lighting")
# set_property(TARGET pbr_theory pbr_textured 
#              PROPERTY FOLDER "6. PBR")

# External libraries folder
set_property(TARGET glfw glad assimp PROPERTY FOLDER "External")

# Default startup project
set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT hello_window)