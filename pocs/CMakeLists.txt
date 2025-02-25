cmake_minimum_required(VERSION 3.13)


# Maps to Visual Studio solution file (Tutorial.sln)
# The solution will have all targets (exe, lib, dll) 
# as Visual Studio projects (.vcproj)
project (pocs)

if (UNIX)     
    enable_language(C ASM_NASM)    
    # Add the assembly file to the library
    add_library(sinkclose_lib STATIC AmdSinkclose/sinkclose.s) 
    set_source_files_properties(
        AmdSinkclose/sinkclose.s 
        PROPERTIES LANGUAGE ASM_NASM
    )
endif (UNIX)

if (WIN32)
    enable_language(ASM_NASM)
    add_library(sinkclose_lib STATIC AmdSinkclose/sinkclose.s) 
    set_source_files_properties(
        AmdSinkclose/sinkclose.s 
        PROPERTIES LANGUAGE ASM_NASM
    )
endif (WIN32)

include_directories (
    "${PROJECT_SOURCE_DIR}/../PlatboxLib/inc"
    "${PROJECT_SOURCE_DIR}/../PlatboxLib/inc/smm"
    "${PROJECT_SOURCE_DIR}/../PlatboxLib/inc/intel"
    "${PROJECT_SOURCE_DIR}/../PlatboxLib/inc/amd"
    "${PROJECT_SOURCE_DIR}/../PlatboxLib/DeltaFuzz"    
)

if (WIN32)
    include_directories ("${PROJECT_SOURCE_DIR}/../PlatboxLib/inc/windows")
endif (WIN32)

if (UNIX)
    include_directories (
        "${PROJECT_SOURCE_DIR}/../PlatboxLib/inc/linux"
        "${PROJECT_SOURCE_DIR}/../PlatboxDrv/linux/include"           
    )
endif (UNIX)

SET (PROJECT_ROOT "${PROJECT_SOURCE_DIR}")

set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /MT")
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /MTd")



# POC executables

add_executable(AcerSpiAccessViaSmiPoc Acer/CVE-2023-28468.cpp)
add_executable(HuaweiSpiAccessViaSmiPoc Huawei/CVE-2023-28468.cpp)
add_executable(AcerSmmCalloutPoc Acer/SmmCallout.cpp)
add_executable(SecureCoreAcer SecureCoreAcer/DirectSpiAccess.cpp)

add_executable(AmdSinkClose  AmdSinkclose/sinkclose.cpp)



# Link libraries
target_link_libraries(AcerSpiAccessViaSmiPoc platbox_lib)
target_link_libraries(HuaweiSpiAccessViaSmiPoc platbox_lib)
target_link_libraries(AcerSmmCalloutPoc platbox_lib)
target_link_libraries(SecureCoreAcer platbox_lib)

target_link_libraries(AmdSinkClose sinkclose_lib platbox_lib -static)
