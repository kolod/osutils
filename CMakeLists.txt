cmake_minimum_required(VERSION 3.12)
project(osutils VERSION 1.0.0 LANGUAGES CXX)

option(OSUTILS_BUILD_EXAMPLES "Build examples tree" OFF)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_STANDARD 14)

find_package(QT NAMES Qt5 Qt6 REQUIRED COMPONENTS Widgets)

message(STATUS "Qt Version: ${QT_VERSION}")
message(STATUS "System: ${CMAKE_SYSTEM_NAME}")

if(CMAKE_SYSTEM_NAME STREQUAL Windows)

	set(OSUTILS_PLATFORM_SOURCECES
		winutils/WinUtils.h
		winutils/WinUtils.cpp
	)

	if(MSVC)
		set(OSUTILS_PLATFORM_LIBS user32)
	endif()

elseif(CMAKE_SYSTEM_NAME STREQUAL Linux)

	set(OSUTILS_PLATFORM_COMPONENTS
		X11Extras
		X11
	)

	set(OSUTILS_PLATFORM_SOURCECES
		nixutils/NixUtils.h
		nixutils/NixUtils.cpp
		nixutils/X11Funcs.h
		nixutils/X11Funcs.cpp
	)

	set(OSUTILS_PLATFORM_LIBS
		Qt${QT_VERSION_MAJOR}::X11Extras
		X11
	)

else()
	message(FATAL_ERROR "Not supported platform: ${CMAKE_SYSTEM_NAME}.")
endif()

set(OSUTILS_COMPONENTS
	Widgets
	${OSUTILS_PLATFORM_COMPONENTS}
)

set(OSUTILS_SOURCECES
	OSUtils.h
	OSUtilsBase.h
	OSUtilsBase.cpp
	${OSUTILS_PLATFORM_SOURCECES}
)

set(OSUTILS_LIBS
	Qt${QT_VERSION_MAJOR}::Widgets
	${OSUTILS_PLATFORM_LIBS}
	${}
)

find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Widgets X11Extras)
add_library(osutils STATIC ${OSUTILS_SOURCECES})
target_link_libraries(osutils PRIVATE ${OSUTILS_LIBS})

if(OSUTILS_BUILD_EXAMPLES)
	add_subdirectory(examples)
endif()
