cmake_minimum_required(VERSION 3.24)

set(PROJECT_NAME cpp_study_template)

project(${PROJECT_NAME} VERSION 0.1.0 LANGUAGES CXX)

option(ENABLE_WARNINGS "Enable extra warnings" ON)
option(ENABLE_WARNINGS_AS_ERRORS "Treat warnings as errors" OFF)
option(BUILD_TESTING "Build tests" ON)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

include(GNUInstallDirs)

add_library(project_options INTERFACE)
target_compile_features(project_options INTERFACE cxx_std_23)
target_include_directories(project_options INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
)

if (MSVC)
    target_compile_options(project_options INTERFACE /W4 /permissive- /EHsc)
    if (ENABLE_WARNINGS_AS_ERRORS)
        target_compile_options(project_options INTERFACE /WX)
    endif()
else()
    target_compile_options(project_options INTERFACE -Wall -Wextra -Wpedantic)
    if (ENABLE_WARNINGS)
        target_compile_options(project_options INTERFACE -Wconversion -Wsign-conversion)
    endif()
    if (ENABLE_WARNINGS_AS_ERRORS)
        target_compile_options(project_options INTERFACE -Werror)
    endif()
endif()

add_library(${PROJECT_NAME}
        src/main.cpp
)

target_link_libraries(${PROJECT_NAME} PUBLIC project_options)
target_include_directories(${PROJECT_NAME} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
)

add_executable(${PROJECT_NAME}_app
    src/main.cpp
)

target_link_libraries(${PROJECT_NAME}_app PRIVATE ${PROJECT_NAME})

install(TARGETS ${PROJECT_NAME} ${PROJECT_NAME}_app
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)