cmake_minimum_required(VERSION 3.0)

set(CMAKE_DEBUG_POSTFIX "d")
project(nanovg VERSION "0.1" LANGUAGES C)

option(NANOVG_BUILD_EXAMPLES "" ON)

set(NANOVG_SRC
    src/fontstash.h
    src/nanovg_gl_utils.h
    src/nanovg_gl.h
    src/nanovg.c
    src/stb_image.h
    src/stb_truetype.h
)

if(MSVC)
    add_definitions("-D_CRT_SECURE_NO_WARNINGS")
endif(MSVC)

include(CheckCCompilerFlag)
check_c_compiler_flag("-Wextra" NANOVG_SUPPORTS_WEXTRA_FLAG)

if(NANOVG_SUPPORTS_WEXTRA_FLAG)
    add_compile_options("-Wextra")
endif()

add_library(nanovg ${NANOVG_SRC})

if(NANOVG_BUILD_EXAMPLES)
endif()

set(TARGETS_EXPORT_NAME nanovg)
set(TARGETS_EXPORT_NAMESPACE "nanovg::")

include(CMakePackageConfigHelpers)

set(NANOVG_CONFIG_PATH "lib/cmake/${TARGETS_EXPORT_NAME}")
set(NANOVG_GENERATED_PATH "${CMAKE_CURRENT_BINARY_DIR}/generated")

configure_package_config_file("cmake/Config.cmake.in"
    "${NANOVG_GENERATED_PATH}/${TARGETS_EXPORT_NAME}Config.cmake"
    INSTALL_DESTINATION "${NANOVG_CONFIG_PATH}"
)

write_basic_package_version_file("${NANOVG_GENERATED_PATH}/${TARGETS_EXPORT_NAME}ConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY ExactVersion
)

install(FILES "${NANOVG_GENERATED_PATH}/${TARGETS_EXPORT_NAME}Config.cmake"
    "${NANOVG_GENERATED_PATH}/${TARGETS_EXPORT_NAME}ConfigVersion.cmake"
    DESTINATION ${NANOVG_CONFIG_PATH}
)

install(TARGETS nanovg
    EXPORT ${TARGETS_EXPORT_NAME}
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION bin
    INCLUDES DESTINATION include
)

install(DIRECTORY src/
    DESTINATION include
    FILES_MATCHING PATTERN "*.h"
)

install(EXPORT ${TARGETS_EXPORT_NAME} FILE "${TARGETS_EXPORT_NAME}Targets.cmake"
    NAMESPACE ${TARGETS_EXPORT_NAMESPACE}
    DESTINATION ${NANOVG_CONFIG_PATH}
)
