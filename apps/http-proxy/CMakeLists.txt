# Copyright (c) 2019 Cisco and/or its affiliates.
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at:
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

cmake_minimum_required(VERSION 3.5 FATAL_ERROR)
set(CMAKE_CXX_STANDARD 14)

set(CMAKE_MODULE_PATH
  ${CMAKE_MODULE_PATH}
  "${CMAKE_SOURCE_DIR}/cmake/Modules/"
)

if (NOT CMAKE_BUILD_TYPE)
  message(STATUS "No build type selected, default to Release")
  set(CMAKE_BUILD_TYPE "Release")
endif ()

find_package(Threads REQUIRED)
include_directories(
  SYSTEM
  ${CMAKE_BINARY_DIR}
  ${LIB${TRANSPORT_LIBRARY}_INCLUDE_DIR}
  http-server
  http-client
)

set(LIB_SOURCE_FILES
  src/ATSConnector.cc
  src/HTTP1.xMessageFastParser.cc
  src/IcnReceiver.cc
)

set(LIB_SERVER_HEADER_FILES
  src/IcnReceiver.h
  src/ATSConnector.h
  src/HTTP1.xMessageFastParser.h
)

set(APP_SOURCE_FILES
  main.cc
)

set(LIBHTTP_PROXY hicnhttpproxy)

build_library(${LIBHTTP_PROXY}
  STATIC
  SOURCES ${LIB_SOURCE_FILES}
  LINK_LIBRARIES ${LIBRARIES}
  DEPENDS ${DEPENDENCIES}
  INCLUDE_DIRS ${LIBTRANSPORT_INCLUDE_DIRS}
)

build_executable(${HTTP_PROXY}
  SOURCES ${APP_SOURCE_FILES}
  LINK_LIBRARIES ${LIBHTTP_PROXY}
  DEPENDS ${LIBHTTP_PROXY}
  COMPONENT ${HICN_APPS}
  DEFINITIONS ${COMPILER_DEFINITIONS}
)