# CMakeLists.txt for <YUV420ToRGBFilter> 
cmake_minimum_required(VERSION 3.11)

# hints from https://github.com/robotology/how-to-export-cpp-library/blob/master/CMakeLists.txt
project(YUV420ToRGBFilter
        LANGUAGES CXX C
        VERSION 0.0.0.0)

SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -D_CRT_SECURE_NO_WARNINGS")

include(FetchContent)

FetchContent_Declare(
  DirectShowExt
  GIT_REPOSITORY https://github.com/CSIR-RTVC/DirectShowExt
)
# Declare dependencies
find_package(DirectShowExt 1.0.0 REQUIRED)

FetchContent_Declare(
  Vpp
  GIT_REPOSITORY https://github.com/CSIR-RTVC/vpp
)
# Declare dependencies
find_package(Vpp 1.0.0 REQUIRED)

SET(FLT_HDRS
resource.h
stdafx.h
VersionInfo.h
YUV420ToRGBFilter.h
YUY2RGBProperties.h
)

SET(FLT_SRCS 
DLLSetup.cpp
stdafx.cpp
YUV420ToRGBFilter.cpp
YUV420ToRGBFilter.def
YUV420ToRGBFilter.rc
)

ADD_LIBRARY(
YUV420ToRGBFilter SHARED ${FLT_SRCS} ${FLT_HDRS})

target_include_directories(YUV420ToRGBFilter
    PUBLIC 
        $<INSTALL_INTERFACE:install>    
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}
)

LINK_DIRECTORIES(
    $ENV{MSSDK}/Lib
)

TARGET_LINK_LIBRARIES (
YUV420ToRGBFilter
DirectShowExt::DirectShowExt
Vpp::Vpp
) 

INSTALL(
  TARGETS YUV420ToRGBFilter
  ARCHIVE DESTINATION bin
  LIBRARY DESTINATION bin
)

IF (REGISTER_DS_FILTERS)
ADD_CUSTOM_COMMAND(
TARGET YUV420ToRGBFilter 
POST_BUILD COMMAND 
regsvr32 /s \"$(TargetPath)\"
)
ENDIF(REGISTER_DS_FILTERS)
