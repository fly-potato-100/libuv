cmake_minimum_required(VERSION 3.13)

### libuv库，使用时直接在上层CMakeLists.txt中add_subdirectory该目录即可
project(libuv C)

### 根据不同BuildType设置选项
if(CMAKE_BUILD_TYPE STREQUAL Debug)
	### Debug
	set(LIBUV_LIB_TYPE_SUBDIR "debug")
else()
	### 其他
	set(LIBUV_LIB_TYPE_SUBDIR "release")
endif()

### 根据不同平台设置选项
if(WIN32)
	### Windows选项
	set(LIB_FILE_NAME "uv_a.lib")
	if(CMAKE_C_COMPILER_ID MATCHES "Clang")
		### 如果使用clang
		set(LIBUV_LIB_BUILD_SUBDIR "win_clang8.0")	# 设置库的子目录名
	elseif(CMAKE_C_COMPILER_ID MATCHES "MSVC")
		### 如果使用msvc
		set(LIBUV_LIB_BUILD_SUBDIR "win_msvc19.22")	# 设置库的子目录名
	else()
		### 其他报错
		message(FATAL_ERROR "FATAL: Invalid build tools!Aborted.")
	endif()
	### 额外的链接选项
	set(EXTRA_LINK_OPT "iphlpapi.lib psapi.lib userenv.lib ws2_32.lib")
elseif(UNIX)
	### Linux选项
	set(LIB_FILE_NAME "libuv_a.a")
	if(CMAKE_C_COMPILER_ID MATCHES "Clang")
		### 如果使用clang
		set(LIBUV_LIB_BUILD_SUBDIR "linux_clang5.0")	# 设置库的子目录名
	elseif(CMAKE_C_COMPILER_ID MATCHES "GNU")
		### 如果使用gcc
		set(LIBUV_LIB_BUILD_SUBDIR "linux_gcc7.3")	# 设置库的子目录名
	else()
		### 其他报错
		message(FATAL_ERROR "FATAL: Invalid build tools!Aborted.")
	endif()
	### 额外的链接选项
	set(EXTRA_LINK_OPT "-pthread -ldl -lrt")
else()
	### 其他报错
	message(FATAL_ERROR "FATAL: Invalid platform!Aborted.")
endif()

add_library(libuv_static STATIC IMPORTED GLOBAL)
set_target_properties(libuv_static PROPERTIES
	IMPORTED_LOCATION "${CMAKE_CURRENT_SOURCE_DIR}/lib/${LIBUV_LIB_BUILD_SUBDIR}/${LIBUV_LIB_TYPE_SUBDIR}/${LIB_FILE_NAME}"
	INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/include"
	)

if(EXTRA_LINK_OPT)
	set_target_properties(libuv_static PROPERTIES INTERFACE_LINK_LIBRARIES ${EXTRA_LINK_OPT})
endif()

if(EXTRA_COMPILE_OPT)
	set_target_properties(libuv_static PROPERTIES INTERFACE_COMPILE_OPTIONS ${EXTRA_COMPILE_OPT})
endif()