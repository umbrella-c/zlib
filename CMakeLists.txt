cmake_minimum_required (VERSION 3.12)

project (zlib C)

option (ZLIB_BUILD_STATIC "Build the static version of zlib" ON)
option (ZLIB_BUILD_SHARED "Build the shared library version of zlib" ON)

set (SRCS "")

macro (add_sources)
    file (RELATIVE_PATH _relPath "${PROJECT_SOURCE_DIR}" "${CMAKE_CURRENT_SOURCE_DIR}")
    foreach (_src ${ARGN})
        if (_relPath)
            list (APPEND SRCS "${_relPath}/${_src}")
        else()
            list (APPEND SRCS "${_src}")
        endif()
    endforeach()
endmacro()

configure_file(zconf.h.in zconf.h)

add_sources(
    src/adler32.c
    src/compress.c
    src/crc32.c
    src/deflate.c
    src/gzclose.c
    src/gzlib.c
    src/gzread.c
    src/gzwrite.c
    src/infback.c
    src/inffast.c
    src/inflate.c
    src/inftrees.c
    src/trees.c
    src/uncompr.c
    src/zutil.c
)

include_directories (
    ${CMAKE_BINARY_DIR}
    include
)

if (ZLIB_BUILD_STATIC)
    add_library(zlib_static STATIC ${SRCS})
    install(TARGETS zlib_static
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
    )
endif ()

if (ZLIB_BUILD_SHARED)
    set (SHARED_SRCS ${SRCS})
    if (MOLLENOS)
        set (SHARED_SRCS ${SHARED_SRCS} main.c)
    endif ()

    add_library(zlib SHARED ${SHARED_SRCS})
    target_compile_definitions(zlib PUBLIC -DZLIB_DLL)
    install(TARGETS zlib
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin
    )
endif ()

install(FILES include/zlib.h ${CMAKE_BINARY_DIR}/zconf.h DESTINATION include)
