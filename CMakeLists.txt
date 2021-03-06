# ============================================================================
#
# phd.fixed_vector
#
# Written in 2019 by ThePhD <phdofthehouse@gmail.com>
#
# To the extent possible under law, the author(s) have dedicated all copyright and related
# and neighboring rights to this software to the public domain worldwide. This software is
# distributed without any warranty.
#
# You should have received a copy of the CC0 Public Domain Dedication along with this software.
# If not, see <http://creativecommons.org/publicdomain/zero/1.0/>.
#
# ============================================================================

cmake_minimum_required(VERSION 3.13.0)

# # Project declaration
# informs about the project, gives a description, version and MOST IMPORTANTLY
# the languages the project is going to use. Required.
project(phd.fixed_vector VERSION 0.3.0 DESCRIPTION "A library for a fixed-capacity vector." LANGUAGES C CXX)

# # Modules
# Include modules useful to the project, whether locally made in our own cmake DIRECTORY
# our from the standard cmake libraries
# Add home-rolled modules path to front of module path list
set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake" "${CMAKE_MODULE_PATH}")

# # # Top-Level Directories
# Check if this is the top-level project or not
if(CMAKE_SOURCE_DIR STREQUAL PROJECT_SOURCE_DIR)
    set(PHD_FIXED_VECTOR_IS_TOP_LEVEL_PROJECT true)
else()
    set(PHD_FIXED_VECTOR_IS_TOP_LEVEL_PROJECT false)
endif()

# Modify bad flags / change defaults if that is the case
if (PHD_FIXED_VECTOR_IS_TOP_LEVEL_PROJECT)
	if (CMAKE_SIZEOF_VOID_P EQUAL 4)
		set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/x86/${CMAKE_BUILD_TYPE}/${CFG_INT_DIR}/lib")
		set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/x86/${CMAKE_BUILD_TYPE}/${CFG_INT_DIR}/bin")
		set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/x86/${CMAKE_BUILD_TYPE}/${CFG_INT_DIR}/bin")
	else()
		set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/x64/${CMAKE_BUILD_TYPE}/${CFG_INT_DIR}/lib")
		set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/x64/${CMAKE_BUILD_TYPE}/${CFG_INT_DIR}/bin")
		set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/x64/${CMAKE_BUILD_TYPE}/${CFG_INT_DIR}/bin")
	endif()

	if (MSVC)
		string(REGEX REPLACE "/W[0-4]" "" CMAKE_C_FLAGS ${CMAKE_C_FLAGS})
		string(REGEX REPLACE "/W[0-4]" "" CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS})
	endif()
endif()

# # Include standard modules
include(CMakeDependentOption)
include(CMakePackageConfigHelpers)
include(GNUInstallDirs)

# # Options
option(PHD_FIXED_VECTOR_CI "Whether or not we are in contiguous integration mode" OFF)
option(PHD_FIXED_VECTOR_TESTS "Enable build of tests" OFF)
option(PHD_FIXED_VECTOR_EXAMPLES "Enable build of examples" OFF)
option(PHD_FIXED_VECTOR_BENCHMARKS "Enable build of benchmarks" OFF)

set(phd.fixed_vector.sources
	"include/phd/fixed_vector/fixed_vector.hpp"

	"include/phd/fixed_vector/detail/type_traits.hpp"
	"include/phd/fixed_vector/detail/uninit.hpp"
	"include/phd/fixed_vector/detail/wrap_pointer.hpp"
)

add_library(phd.fixed_vector INTERFACE)
add_library(phd::fixed_vector ALIAS phd.fixed_vector)
target_sources(phd.fixed_vector INTERFACE ${phd.fixed_vector.sources})
target_link_libraries(phd.fixed_vector)
target_include_directories(phd.fixed_vector INTERFACE 
	$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>  
	$<INSTALL_INTERFACE:include>
)
if (PHD_FIXED_VECTOR_IS_TOP_LEVEL_PROJECT)
	if (MSVC)
		target_compile_options(phd.fixed_vector INTERFACE 
			/std:c++latest
			/permissive-
		)
	else()
		target_compile_options(phd.fixed_vector INTERFACE 
			-std=c++2a
		)
	endif()
endif()
#set_target_properties(phd.fixed_vector
#	PROPERTIES
#	EXPORT_NAME phd::fixed_vector
#)

# # Benchmarks, Tests, Examples
if (PHD_FIXED_VECTOR_TESTS)
	enable_testing()
endif()
if (PHD_FIXED_VECTOR_TESTS)
	enable_testing()
	add_subdirectory(tests)
endif()
if (PHD_FIXED_VECTOR_EXAMPLES)
	add_subdirectory(examples)
endif()
if (PHD_FIXED_VECTOR_BENCHMARKS)
	add_subdirectory(benchmarks)
endif()
