#   Copyright (c) 2022 StoneAtom, Inc. All rights reserved.
#   Use is subject to license terms
#
#   This program is free software; you can redistribute it and/or modify
#   it under the terms of the GNU General Public License as published by
#   the Free Software Foundation; version 2 of the License.
#
#   This program is distributed in the hope that it will be useful,
#   but WITHOUT ANY WARRANTY; without even the implied warranty of
#   MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#   GNU General Public License for more details.
#
#   You should have received a copy of the GNU General Public License
#   along with this program; if not, write to the Free Software
#   Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA  02110-1335  USA


CMAKE_MINIMUM_REQUIRED(VERSION 3.7)

SET(STONEDB_PLUGIN_STATIC  "stonedb")
SET(STONEDB_PLUGIN_MANDATORY  TRUE)

MESSAGE(STATUS "Using cmake version ${CMAKE_VERSION}")
MESSAGE(STATUS "Building StoneDB")

#lihao Here, the dependency libs will be moved to 'third_party' directory.
SET(BOOST_ROOT /usr/local/stonedb-boost)
SET(MARISA_ROOT /usr/local/stonedb-marisa)
SET(ROCKSDB_ROOT /usr/local/stonedb-gcc-rocksdb)

SET(LINK_LIBS ${BOOST_ROOT}/lib/libboost_system.a
              stdc++fs
              snappy
              bz2
              lz4
              zstd
#              /opt/rh/devtoolset-7/root/usr/lib/gcc/x86_64-redhat-linux/7/libstdc++fs.a
              ${MARISA_ROOT}/lib/libmarisa.a
              ${ROCKSDB_ROOT}/lib/librocksdb.a
              ${BOOST_ROOT}/lib/libboost_thread.a)

AUX_SOURCE_DIRECTORY(common SOURCE_common)
AUX_SOURCE_DIRECTORY(compress SOURCE_compress)
AUX_SOURCE_DIRECTORY(core SOURCE_core)
AUX_SOURCE_DIRECTORY(exporter SOURCE_exporter)
AUX_SOURCE_DIRECTORY(handler SOURCE_handler)
AUX_SOURCE_DIRECTORY(loader SOURCE_loader)
AUX_SOURCE_DIRECTORY(mm SOURCE_mm)
AUX_SOURCE_DIRECTORY(mm/tcm SOURCE_mm_tcm)
AUX_SOURCE_DIRECTORY(system SOURCE_system)
AUX_SOURCE_DIRECTORY(types SOURCE_types)
AUX_SOURCE_DIRECTORY(util SOURCE_util)
AUX_SOURCE_DIRECTORY(vc SOURCE_vc)
AUX_SOURCE_DIRECTORY(index SOURCE_index)
AUX_SOURCE_DIRECTORY(base/core SOURCE_base_core)
AUX_SOURCE_DIRECTORY(base/fmt SOURCE_base_fmt)
AUX_SOURCE_DIRECTORY(base/net SOURCE_base_net)
AUX_SOURCE_DIRECTORY(base/util SOURCE_base_util)

SET(STONEDB_SOURCES ${SOURCE_common}
			${SOURCE_compress}
			${SOURCE_core}
			${SOURCE_exporter}
			${SOURCE_handler}
			${SOURCE_loader}
			${SOURCE_mm}
			${SOURCE_mm_tcm}
			${SOURCE_system}
			${SOURCE_types}
			${SOURCE_util}
			${SOURCE_vc}
			${SOURCE_index}
			${SOURCE_base_core}
			${SOURCE_base_fmt}
			${SOURCE_base_net}
			${SOURCE_base_util})

MYSQL_ADD_PLUGIN(stonedb ${STONEDB_SOURCES}
			STORAGE_ENGINE
			MANDATORY
			STATIC_ONLY
			LINK_LIBRARIES ${LINK_LIBS})

TARGET_INCLUDE_DIRECTORIES(stonedb SYSTEM PRIVATE ${CMAKE_SOURCE_DIR}/include)
TARGET_INCLUDE_DIRECTORIES(stonedb SYSTEM PRIVATE ${CMAKE_SOURCE_DIR}/sql)
TARGET_INCLUDE_DIRECTORIES(stonedb SYSTEM PRIVATE ${CMAKE_SOURCE_DIR}/regex)
TARGET_INCLUDE_DIRECTORIES(stonedb SYSTEM PRIVATE ${SSL_INCLUDE_DIRS})
TARGET_INCLUDE_DIRECTORIES(stonedb SYSTEM PRIVATE ${ZLIB_INCLUDE_DIR})
TARGET_INCLUDE_DIRECTORIES(stonedb SYSTEM PRIVATE ${ROCKSDB_ROOT}/include)
TARGET_INCLUDE_DIRECTORIES(stonedb SYSTEM PRIVATE ${BOOST_ROOT}/include)
TARGET_INCLUDE_DIRECTORIES(stonedb SYSTEM PRIVATE ${MARISA_ROOT}/include)
TARGET_INCLUDE_DIRECTORIES(stonedb PRIVATE ${CMAKE_SOURCE_DIR}/storage/stonedb)
TARGET_INCLUDE_DIRECTORIES(stonedb PRIVATE ${CMAKE_SOURCE_DIR}/storage/stonedb/base)
#Wno-error for release build
TARGET_COMPILE_OPTIONS(stonedb PRIVATE -Wno-error -Wextra -Wall)
TARGET_COMPILE_OPTIONS(stonedb PRIVATE -Wunused-parameter)
TARGET_COMPILE_OPTIONS(stonedb PRIVATE -Wsuggest-override)

add_subdirectory(async_tests)