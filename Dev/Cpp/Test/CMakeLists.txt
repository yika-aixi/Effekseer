# Debug Releaseに限定
set(CMAKE_CONFIGURATION_TYPES Debug Release CACHE TYPE INTERNAL FORCE )

# プロジェクト名
project(Test)

# CMake バージョンチェック
cmake_minimum_required(VERSION 2.8)

# コンパイル設定
if (MSVC)
	# 特に何も
else()
	set(CMAKE_CXX_FLAGS "-std=c++11" ${CMAKE_CXX_FLAGS})
endif()

# ソースコード設定
set(effekseer_src
	graphics_gl.cpp
	sound_al.cpp
#	window.cpp
	main.cpp)

# インクルードの設定
include_directories(
	../Effekseer/
	../EffekseerRendererGL/
	../EffekseerSoundAL/
	)

# 
link_directories(${PROJECT_SOURCE_DIR}/../lib/)

set(common_lib)
set(common_inc)

list(APPEND common_lib Effekseer )
list(APPEND common_lib EffekseerRendererGL )
list(APPEND common_lib EffekseerSoundAL )

list(APPEND common_lib glfw3 )

if (MSVC)

else()
	list(APPEND common_lib GLEW )

	find_package(PNG REQUIRED)
	list(APPEND common_lib ${PNG_LIBRARY})

	find_package(Threads REQUIRED)
	list(APPEND common_lib ${CMAKE_THREAD_LIBS_INIT})

	find_package(X11 REQUIRED)
	list(APPEND common_inc ${X11_INCLUDE_DIR})
	list(APPEND common_lib ${X11_LIBRARIES})

	find_package(OpenGL)
	list(APPEND common_inc ${OPENGL_INCLUDE_DIR})
	list(APPEND common_lib ${OPENGL_LIBRARY})

	find_package(OpenAL)
	list(APPEND common_inc ${OPENAL_INCLUDE_DIR})
	list(APPEND common_lib ${OPENAL_LIBRARY})

 	if(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
		# used in GLFW
		find_library(COCOA_FRAMEWORK Cocoa)
		find_library(COCOA_FRAMEWORK Cocoa)
		find_library(IOKIT_FRAMEWORK IOKit)
		find_library(CORE_FOUNDATION_FRAMEWORK CoreFoundation)
		find_library(CORE_VIDEO_FRAMEWORK CoreVideo)
		list(APPEND common_lib
			${COCOA_FRAMEWORK}
			${OPENGL_gl_LIBRARY}
			${OPENGL_glu_LIBRARY}
			${IOKIT_FRAMEWORK}
			${CORE_FOUNDATION_FRAMEWORK}
			${CORE_VIDEO_FRAMEWORK})
	endif()

endif()

# コンパイル先を追加
add_executable( effekseer ${effekseer_src} )
target_link_libraries(effekseer ${common_lib}) 

# フラグ処理
add_definitions(-D__EFFEKSEER_RENDERER_GL_GLEW__)

add_definitions(-D__EFFEKSEER_RENDERER_INTERNAL_LOADER__)

if (APPLE)
	add_definitions(-D__EFFEKSEER_RENDERER_GL3__)
endif()

# 出力先の設定
if (MSVC)
set_target_properties(effekseer PROPERTIES OUTPUT_NAME_DEBUG "Test" )
set_target_properties(effekseer PROPERTIES OUTPUT_NAME_RELEASE "Test" )
else()
set_target_properties(effekseer PROPERTIES OUTPUT_NAME "Test" )
endif()

install(
	TARGETS effekseer
	RUNTIME DESTINATION bin
	ARCHIVE DESTINATION bin
	LIBRARY DESTINATION bin)

