cmake_minimum_required(VERSION 2.8)
project (libgif)

if (DEFINED CMAKE_TOOLCHAIN_FILE)
	message("Include Cross Platform cmake: " ${CROSS_PLATFORM_CMAKE})
	get_filename_component(COMPILER_NAME ${CMAKE_C_COMPILER} NAME)
	message( "CMAKE_C_COMPILER: ${COMPILER_NAME}")
	string(REGEX REPLACE "(.*)-gcc" "\\1" target_platform "${COMPILER_NAME}")
	message( "target_platform: ${target_platform}")
	set(target_platform_preconfigure_options "CC=${target_platform}-gcc;AR=${target_platform}-ar;LD=${target_platform}-ld;RANLIB=${target_platform}-ranlib;NM=${target_platform}-nm;AS=${target_platform}-as;OBJDUMP=${target_platform}-objdump;OBJCOPY=${target_platform}-objcopy;STRIP=${target_platform}-strip;STRINGS=${target_platform}-strings;SIZE=${target_platform}-size")
endif(DEFINED CMAKE_TOOLCHAIN_FILE)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

set(TOOL_LIBGIF_SOURCE giflib-5.0.4.tar.bz2)
set(TOOL_LIBGIF_DIR giflib-5.0.4)
set(CONFIG_ADD_OPTIONS "--enable-static;--enable-shared")
	if(DEFINED CMAKE_TOOLCHAIN_FILE)
		add_custom_command(
			OUTPUT BUILD_LIBGIF
			COMMAND wget -qO- http://downloads.sourceforge.net/project/giflib/giflib-5.x/${TOOL_LIBGIF_SOURCE} | tar -xjvf - && cd ${CMAKE_BINARY_DIR}/${TOOL_LIBGIF_DIR} && ${target_platform_preconfigure_options} ./configure --host=${target_platform} ${CONFIG_ADD_OPTIONS} && make -j2 && mkdir -p ${CMAKE_LIBRARY_OUTPUT_DIRECTORY} && cp -l ${CMAKE_BINARY_DIR}/${TOOL_LIBGIF_DIR}/./lib/.libs/lib* ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}
			WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
			COMMENT "Build libgif"
		)
	else(DEFINED CMAKE_TOOLCHAIN_FILE)
		add_custom_command(
			OUTPUT BUILD_LIBGIF
			COMMAND wget -qO- http://downloads.sourceforge.net/project/giflib/giflib-5.x/${TOOL_LIBGIF_SOURCE} | tar -xjvf - && cd ${CMAKE_BINARY_DIR}/${TOOL_LIBGIF_DIR} && ./configure ${CONFIG_ADD_OPTIONS} && make -j2 && mkdir -p ${CMAKE_LIBRARY_OUTPUT_DIRECTORY} && cp -l ${CMAKE_BINARY_DIR}/${TOOL_LIBGIF_DIR}/./lib/.libs/lib* ${CMAKE_LIBRARY_OUTPUT_DIRECTORY} 
			WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
			COMMENT "Build libgif" 
		)
	endif(DEFINED CMAKE_TOOLCHAIN_FILE)
	add_custom_target(build_libgif ALL DEPENDS BUILD_LIBGIF )
