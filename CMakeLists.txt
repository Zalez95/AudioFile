cmake_minimum_required(VERSION 3.0.2 FATAL_ERROR)

###############################################################################
# 								AudioFile
###############################################################################
# Project name and version
project(AudioFile LANGUAGES CXX)
set(AUDIOFILE_VERSION_MAJOR "1")
set(AUDIOFILE_VERSION_MINOR "0")
set(AUDIOFILE_VERSION_REVISION "2")
set(AUDIOFILE_VERSION "${AUDIOFILE_VERSION_MAJOR}.${AUDIOFILE_VERSION_MINOR}.${AUDIOFILE_VERSION_REVISION}")

# Project options
option(AUDIOFILE_BUILD_TESTS "Build the AudioFile tests" OFF)

# Find the library source files
file(GLOB_RECURSE AUDIOFILE_SOURCES "src/*.cpp")

# Create the library
add_library(AudioFile ${AUDIOFILE_SOURCES})

target_include_directories(AudioFile PUBLIC
	$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
	$<INSTALL_INTERFACE:include>
)

# Add the compiler options
set_target_properties(AudioFile PROPERTIES
	CXX_STANDARD			11
	CXX_STANDARD_REQUIRED	On
	DEBUG_POSTFIX			"d"
)
if(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
	target_compile_options(AudioFile PRIVATE -Wall -Werror -Wpedantic -Wno-sign-compare)
elseif(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
	target_compile_options(AudioFile PRIVATE /W4 -D_CRT_SECURE_NO_WARNINGS)

	option(FORCE_STATIC_VCRT "Force /MT for static VC runtimes" OFF)
	if(FORCE_STATIC_VCRT)
		foreach(flagVar
			CMAKE_CXX_FLAGS CMAKE_CXX_FLAGS_DEBUG CMAKE_CXX_FLAGS_RELEASE
			CMAKE_CXX_FLAGS_MINSIZEREL CMAKE_CXX_FLAGS_RELWITHDEBINFO
		)
			if(${flagVar} MATCHES "/MD")
				string(REGEX REPLACE "/MD" "/MT" ${flagVar} "${${flagVar}}")
			endif()
		endforeach()
	endif()
endif()

# Install the library
install(TARGETS AudioFile EXPORT AudioFileTargets
	LIBRARY DESTINATION lib
	ARCHIVE DESTINATION lib
	RUNTIME DESTINATION bin
	INCLUDES DESTINATION include
)
install(EXPORT AudioFileTargets
	FILE AudioFileTargets.cmake
	NAMESPACE AudioFile::
	DESTINATION lib/cmake/AudioFile
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file("AudioFileConfigVersion.cmake"
	VERSION ${AUDIOFILE_VERSION}
	COMPATIBILITY SameMajorVersion
)
install(FILES "AudioFileConfig.cmake" "AudioFileConfigVersion.cmake"
	DESTINATION lib/cmake/AudioFile
)

# Create the tests
if(AUDIOFILE_BUILD_TESTS)
	message(FATAL_ERROR "Tests not implemented yet")
endif()
