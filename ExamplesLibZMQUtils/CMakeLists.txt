# **********************************************************************************************************************
#    LibZMQUtils (ZeroMQ High Level Utilities C++ Library).
#
#    ExamplesLibDegorasSLR related project.
#
#    A modern open-source C++ library with high level utilities based on the well-known ZeroMQ open-source universal
#    messaging library. Includes custom command based server-client and publisher-subscriber with automatic binary
#    serialization capabilities, specially designed for system infraestructure. Developed as a free software under the
#    context of Degoras Project for the Spanish Navy Observatory SLR station (SFEL) in San Fernando and, of course,
#    for any other station that wants to use it!
#
#    Copyright (C) 2024 Degoras Project Team
#                       < Ángel Vera Herrera, avera@roa.es - angeldelaveracruz@gmail.com >
#                       < Jesús Relinque Madroñal >
#
#    This file is part of LibZMQUtils.
#
#    Licensed under the European Union Public License (EUPL), Version 1.2 or subsequent versions of the EUPL license
#    as soon they will be approved by the European Commission (IDABC).
#
#    This project is free software: you can redistribute it and/or modify it under the terms of the EUPL license as
#    published by the IDABC, either Version 1.2 or, at your option, any later version.
#
#    This project is distributed in the hope that it will be useful. Unless required by applicable law or agreed to in
#    writing, it is distributed on an "AS IS" basis, WITHOUT ANY WARRANTY OR CONDITIONS OF ANY KIND; without even the
#    implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the EUPL license to check specific
#    language governing permissions and limitations and more details.
#
#    You should use this project in compliance with the EUPL license. You should have received a copy of the license
#    along with this project. If not, see the license at < https://eupl.eu/ >.
# **********************************************************************************************************************

# **********************************************************************************************************************
# Updated 24/04/2024
# **********************************************************************************************************************

# **********************************************************************************************************************
# LIBZMQUTILS EXAMPLES MAIN PROJECT CMAKELIST
# **********************************************************************************************************************

# ----------------------------------------------------------------------------------------------------------------------
# INITIALIZATION

# Cmake minimum and empty project for avoid warnings.
cmake_minimum_required(VERSION 3.21)
project(_)

# Set path to additional CMake modules.
set(CMAKE_MODULE_PATH
    ${CMAKE_SOURCE_DIR}/CMakeModules ${CMAKE_MODULE_PATH}
    ${CMAKE_SOURCE_DIR}/CMakeLibsConfig ${CMAKE_MODULE_PATH})

# Init CMake modules.
include(CMakeMacrosGlobals)
macro_cmakemodules_init("24.04.22")

# ----------------------------------------------------------------------------------------------------------------------
# BASIC PROJECT CONFIGURATION

# Base project configuration.
set(PROJ_NAME ExamplesLibZMQUtils)
set(PROJ_VER 1.6.6)
set(PROJ_BUILD_MODE Debug)
set(PROJ_INSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../${PROJ_NAME}_product_v${PROJ_VER})
set(PROJ_LANGUAGES CXX C)

# Macros global configuration.
macro_global_set_show_externals(TRUE)
macro_global_set_force_install_dir(TRUE)
macro_global_set_install_runtime_deps(TRUE)
macro_global_set_install_runtime_artifacts(TRUE)
macro_global_set_exclude_so_libraries(TRUE)

# Setup the base project.
macro_setup_base_project(${PROJ_NAME} ${PROJ_VER} ${PROJ_BUILD_MODE} ${PROJ_LANGUAGES})

# Other configurations.
set(CMAKE_CXX_STANDARD 17)

# Setup the install directory for each compiler and operating system.
macro_setup_default_install_dir(${PROJ_INSTALL_DIR})

# Set the installation example path.
set(GLOBAL_LIBZMQUTILS_EXAMPLES_INSTALL_PATH ${MODULES_GLOBAL_INSTALL_BIN_PATH})

# Define the global dependency set name.
macro_global_set_main_dep_set_name("${PROJ_NAME}_deps")

# ----------------------------------------------------------------------------------------------------------------------
# SPECIFIC PROJECT CONFIGURATION

# WARNING QT CREATOR BUG
set(AVOID_QTCREATOR13_DLL_SEARCH_BUG TRUE)

# Configure the LibZMQUtils package.
include(ConfigLibZMQUtils)
macro_configure_libzmqutils_default("1.6.6" "" "" "")

# Global include the LibZMQUtils directories.
include_directories(${LibZMQUtils_INCLUDES})

# Add external libraries location paths to global scope.
macro_global_add_libs_full_paths("${LibZMQUtils_LOCATION}" "${LibZMQUtils_LOCATION_DEPENDENCIES}")
macro_global_add_libs_folders("${LibZMQUtils_LOCATION_DIR}" "${LibZMQUtils_LOCATION_DEPENDENCIES_DIR}")

# Add the LibZMQUtils to global link variable.
# NOTE You can comment this and link each launcher in a separated way.
#macro_global_add_libs_auto(${LibZMQUtils_LOCATION})

# ----------------------------------------------------------------------------------------------------------------------
# CONFIGURE THE COMPILERS

# Configure the compilers.
if (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    if (CMAKE_BUILD_TYPE STREQUAL "Debug")
        set(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -Wpedantic -Wall -Wextra -O0 -fopenmp")
    else()
        set(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -pedantic -Wall -Wextra -m64 -O3 -pthread -fopenmp")
    endif()
else()
    message(FATAL_ERROR "Compiler not supported by default.")
endif()

# ----------------------------------------------------------------------------------------------------------------------
# INSTALLATION PROCESS

# External libs locations.
#set(ext_libs_loc ${LibZMQUtils_LOCATION_DEPENDENCIES_DIR} ${LibZMQUtils_LOCATION_DIR})

# ----------------------------------------------------------------------------------------------------------------------
# SETUP THE EXAMPLES

add_subdirectory(PublisherSubscriber)
add_subdirectory(CommandServerClient)

# **********************************************************************************************************************

# **********************************************************************************************************************

# WARNING Only for avoid the QTCREATOR 13 BUG WHEN EXECUTING LAUNCHERS.
if (WIN32 AND AVOID_QTCREATOR13_DLL_SEARCH_BUG)
    message(STATUS "Re-installing runtime deps due to QTCreator 13 bug...")
    message(STATUS "  Runtime deps: ${MODULES_GLOBAL_LIBS_FULL_PATHS}")
    foreach(deploy_dir IN LISTS MODULES_GLOBAL_LAUNCHER_DEPLOYS_DIRS)
        message(STATUS "  Processing launchers deployment directory: ${deploy_dir}")
        install(FILES ${MODULES_GLOBAL_LIBS_FULL_PATHS} DESTINATION "${deploy_dir}")
    endforeach()
endif()

# **********************************************************************************************************************
