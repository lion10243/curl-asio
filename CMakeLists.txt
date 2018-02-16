# curlasio
# Seamlessly integrate libcurl with Boost.Asio
#
# copyright (c) 2013 Oliver Kuckertz <oliver.kuckertz@mologie.de>
# See COPYING for license information.
cmake_minimum_required(VERSION 3.2 FATAL_ERROR)

include( "cmake/HunterGate.cmake" )

HunterGate(
    URL "https://github.com/ruslo/hunter/archive/v0.20.9.tar.gz"
    SHA1 "8f7cf6d719d05c49224a4ebeba90c6bb90b90f4a"
)

project(curlasio VERSION 0.9 LANGUAGES CXX)

include(GNUInstallDirs)

hunter_add_package(CURL)
find_package(CURL CONFIG REQUIRED)

hunter_add_package(Boost COMPONENTS system)
find_package(Boost CONFIG REQUIRED system)

add_library(curlasio
	src/error_code.cpp
	src/form.cpp
	src/share.cpp
	src/string_list.cpp
	src/easy.cpp
	src/initialization.cpp
	src/multi.cpp
)

target_include_directories(curlasio 
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:include>
)

target_compile_features(curlasio
    PUBLIC 
        cxx_std_14
)

target_link_libraries(curlasio
	PUBLIC
		CURL::libcurl
		Boost::boost
		Boost::system
)

install(TARGETS curlasio EXPORT curlasioConfig
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
)
install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
install(EXPORT curlasioConfig DESTINATION cmake)
export(TARGETS curlasio FILE curlasioConfig.cmake)

option(BUILD_EXAMPLES "build examples" OFF)

if (BUILD_EXAMPLES)
	add_executable(asynchronous examples/asynchronous.cpp)
	target_link_libraries(asynchronous 
		PRIVATE 
			curlasio
	)

	add_executable(synchronous examples/synchronous.cpp)
	target_link_libraries(synchronous 
		PRIVATE 
			curlasio
	)
endif()
