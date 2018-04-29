cmake_minimum_required(VERSION 3.0.2 FATAL_ERROR)

###############################################################################
# 								AudioFile
###############################################################################
# Project name and version
project(AudioFile VERSION 1.0.2 LANGUAGES CXX)

# Project options
option(AUDIOFILE_BUILD_TESTS "Build the AudioFile tests" ON)

# Set the project output directories
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_SOURCE_DIR}/bin")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_SOURCE_DIR}/lib")
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_SOURCE_DIR}/lib")
foreach(outputConfig ${CMAKE_CONFIGURATION_TYPES})
	set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_${outputConfig} "${CMAKE_SOURCE_DIR}/bin")
	set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_${outputConfig} "${CMAKE_SOURCE_DIR}/lib")
	set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_${outputConfig} "${CMAKE_SOURCE_DIR}/lib")
endforeach()

# Find the library source files
file(GLOB_RECURSE AUDIOFILE_SOURCES "src/*.cpp")

# Create the library
add_library(audioFile ${AUDIOFILE_SOURCES})

target_include_directories(audioFile PUBLIC
	$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
	$<INSTALL_INTERFACE:include>
)

# Add the compiler options
set_target_properties(audioFile PROPERTIES
	CXX_STANDARD			11
	CXX_STANDARD_REQUIRED	On
	DEBUG_POSTFIX			"d"
)
if(${CMAKE_CXX_COMPILER_ID} MATCHES "GNU")
	target_compile_options(audioFile PRIVATE -Wall -Werror -Wpedantic -Wno-sign-compare)
elseif(${CMAKE_CXX_COMPILER_ID} MATCHES "MSVC")
	target_compile_options(audioFile PRIVATE /W4 -D_CRT_SECURE_NO_WARNINGS)
endif()

# Install the library
install(TARGETS audioFile
	LIBRARY DESTINATION lib
	ARCHIVE DESTINATION lib
)
install(FILES "include/AudioFile.h"
	DESTINATION include
)

# Create the tests
if(AUDIOFILE_BUILD_TESTS)
	message(FATAL_ERROR "Tests not implemented yet")
endif()
