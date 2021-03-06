SET(CMAKE_SYSTEM_NAME Linux)
SET(CMAKE_SYSTEM_PROCESSOR "x86-64")

SET(CMAKE_C_COMPILER clang)
SET(CMAKE_CXX_COMPILER clang++)
SET(CMAKE_SKIP_RPATH TRUE)

project(darling)

cmake_minimum_required(VERSION 2.8)
enable_language(ASM)

set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")
include(InstallSymlink)
include(MacroEnsureOutOfSourceBuild)
include(dsym)
include(xcproj)

MACRO_ENSURE_OUT_OF_SOURCE_BUILD()

set(DARLING_TOP_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}")
set(DARLING_NO_EXECUTABLES OFF)
set(CMAKE_C_IMPLICIT_LINK_LIBRARIES "")
set(CMAKE_CXX_IMPLICIT_LINK_LIBRARIES "")

if (${CMAKE_HOST_SYSTEM_PROCESSOR} MATCHES "i686|i386")
	message(FATAL_ERROR "This software can only be built on x86-64 systems")
endif (${CMAKE_HOST_SYSTEM_PROCESSOR} MATCHES "i686|i386")

SET(IGNORED_WARNINGS "-Wno-nullability-completeness -Wno-deprecated-declarations")

if (${CMAKE_C_COMPILER_ID} STREQUAL "Clang" AND NOT ${CMAKE_C_COMPILER_VERSION} VERSION_LESS "3.9")
	SET(IGNORED_WARNINGS "${IGNORED_WARNINGS} -Wno-expansion-to-defined")
endif (${CMAKE_C_COMPILER_ID} STREQUAL "Clang" AND NOT ${CMAKE_C_COMPILER_VERSION} VERSION_LESS "3.9")

SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${IGNORED_WARNINGS}")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${IGNORED_WARNINGS}")
SET(CMAKE_C_FLAGS_DEBUG "-O0 -ggdb")
SET(CMAKE_CXX_FLAGS_DEBUG "-O0 -ggdb")

enable_language(ASM-ATT)

option(FRAMEWORK_COREAUDIO "Enable audio-related functionality" OFF)
option(FRAMEWORK_APPKIT "Enable AppKit development code" OFF)
option(FULL_BUILD "Include large items in the build" ON)

FindDsymutil()

add_subdirectory(src)

install(DIRECTORY DESTINATION libexec/darling/private)
install(DIRECTORY DESTINATION libexec/darling/private/etc)
install(DIRECTORY DESTINATION libexec/darling/private/var)
install(DIRECTORY DESTINATION libexec/darling/private/tmp)
InstallSymlink(private/etc ${CMAKE_INSTALL_PREFIX}/libexec/darling/etc)
InstallSymlink(private/var ${CMAKE_INSTALL_PREFIX}/libexec/darling/var)

install(FILES etc/resolv.conf
	DESTINATION libexec/darling/etc)
InstallSymlink(/Volumes/SystemRoot/etc/machine-id ${CMAKE_INSTALL_PREFIX}/libexec/darling/etc/machine-id)
InstallSymlink(/Volumes/SystemRoot/etc/nsswitch.conf ${CMAKE_INSTALL_PREFIX}/libexec/darling/etc/nsswitch.conf)

install(DIRECTORY DESTINATION libexec/darling/Volumes)
install(DIRECTORY DESTINATION libexec/darling/Volumes/SystemRoot)
InstallSymlink(/ ${CMAKE_INSTALL_PREFIX}/libexec/darling/Volumes/DarlingEmulatedDrive)

install(DIRECTORY DESTINATION libexec/darling/proc)

install(DIRECTORY DESTINATION libexec/darling/var/root)
install(DIRECTORY DESTINATION libexec/darling/var/run)
InstallSymlink(/dev/log ${CMAKE_INSTALL_PREFIX}/libexec/darling/var/run/syslog)

install(DIRECTORY DESTINATION libexec/darling/usr)
install(DIRECTORY DESTINATION libexec/darling/usr/local)
install(DIRECTORY DESTINATION libexec/darling/usr/local/share)

InstallSymlink(/Volumes/SystemRoot/dev ${CMAKE_INSTALL_PREFIX}/libexec/darling/dev)
InstallSymlink(private/tmp ${CMAKE_INSTALL_PREFIX}/libexec/darling/tmp)
InstallSymlink(/Volumes/SystemRoot/home ${CMAKE_INSTALL_PREFIX}/libexec/darling/Users)
InstallSymlink(Users ${CMAKE_INSTALL_PREFIX}/libexec/darling/home)

InstallSymlink(/proc/self/mounts ${CMAKE_INSTALL_PREFIX}/libexec/darling/etc/mtab)
InstallSymlink(/Volumes/SystemRoot/etc/passwd ${CMAKE_INSTALL_PREFIX}/libexec/darling/etc/passwd)
InstallSymlink(/Volumes/SystemRoot/etc/group ${CMAKE_INSTALL_PREFIX}/libexec/darling/etc/group)
InstallSymlink(/Volumes/SystemRoot/etc/localtime ${CMAKE_INSTALL_PREFIX}/libexec/darling/etc/localtime)

install(DIRECTORY DESTINATION libexec/darling/etc/ld.so.conf.d)
install(CODE "execute_process(COMMAND bash ${DARLING_TOP_DIRECTORY}/src/setup-ld-so.sh WORKING_DIRECTORY ${CMAKE_INSTALL_PREFIX}/libexec/darling)")
install(DIRECTORY DESTINATION libexec/darling/etc/fonts/conf.d)
install(CODE "execute_process(COMMAND bash ${DARLING_TOP_DIRECTORY}/src/setup-fonts.sh WORKING_DIRECTORY ${CMAKE_INSTALL_PREFIX}/libexec/darling)")

InstallSymlink(/Volumes/SystemRoot/lib ${CMAKE_INSTALL_PREFIX}/libexec/darling/lib)
InstallSymlink(/Volumes/SystemRoot/lib64 ${CMAKE_INSTALL_PREFIX}/libexec/darling/lib64)
InstallSymlink(/Volumes/SystemRoot/usr/lib64 ${CMAKE_INSTALL_PREFIX}/libexec/darling/usr/lib64)

InstallSymlink(/Volumes/SystemRoot/usr/share/zoneinfo ${CMAKE_INSTALL_PREFIX}/libexec/darling/usr/share/zoneinfo)
