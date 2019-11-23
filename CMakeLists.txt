project(Hello_world)        # Module name
set(MAIN meta_api.cpp)      # Entry point

cmake_minimum_required(VERSION 3.12)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_GENERATOR_PLATFORM Win32)
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
set(CMAKE_CXX_FLAGS "-m32")

MACRO(HEADER_DIRECTORIES return_list)
    FILE(GLOB_RECURSE new_list "include/*.h")
    SET(dir_list "")
    FOREACH(file_path ${new_list})
        GET_FILENAME_COMPONENT(dir_path ${file_path} PATH)
        SET(dir_list ${dir_list} ${dir_path})
    ENDFOREACH()
    LIST(REMOVE_DUPLICATES dir_list)
    SET(${return_list} ${dir_list})
ENDMACRO()

HEADER_DIRECTORIES(header_dir_list)

list(LENGTH header_dir_list header_dir_list_count)
message(STATUS " >> [INFO] Found ${header_dir_list_count} header directories.")

file(GLOB_RECURSE SRCS "src/*.cpp")

add_library(${PROJECT_NAME}
    SHARED
    ${SRCS}
)

target_include_directories(${PROJECT_NAME}
    PUBLIC
    ${header_dir_list} # Recursive
)


if(CMAKE_CXX_COMPILER_ID MATCHES GNU)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fvisibility=hidden -s -ffunction-sections -fdata-sections -fpermissive -Wno-write-strings -Wno-int-to-pointer-cast -Wno-unused-result -Wno-multichar -D_vsnprintf=vsnprintf")
    set(CMAKE_CXX_FLAGS_DEBUG "-O0 -g3")
    set(CMAKE_CXX_FLAGS_RELEASE "-Os -shared -s")
    set(CMAKE_CXX_FLAGS_MINSIZEREL "-Os -shared -s")
    set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -static-libstdc++")
    set_target_properties(${PROJECT_NAME} PROPERTIES COMPILE_FLAGS "-m32" LINK_FLAGS "-m32" POSITION_INDEPENDENT_CODE ON)
else()
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
    # set( CMAKE_SHARED_LINKER_FLAGS "/EXPORT:GiveFnptrsToDll=_GiveFnptrsToDll@8" )
endif()

set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "")
set_target_properties(${PROJECT_NAME}
    PROPERTIES OUTPUT_NAME "${PROJECT_NAME}_mm"
)