 cmake_minimum_required(VERSION 3.6)

include(ExternalProject)

set(Boost_Bootstrap_Command "./bootstrap.sh")
set(Boost_b2_Command "./b2")

ExternalProject_Add(boost
  URL
    "https://sourceforge.net/projects/boost/files/boost/1.61.0/boost_1_61_0.tar.gz/download"
  URL_MD5
    874805ba2e2ee415b1877ef3297bf8ad

  BUILD_IN_SOURCE ON
  UPDATE_COMMAND ""
  PATCH_COMMAND ""
  CONFIGURE_COMMAND ${Boost_Bootstrap_Command}
  BUILD_COMMAND
      ${Boost_b2_Command}
      install
      --link=static
      --with-date_time
      --with-filesystem
      --with-iostreams
      --with-program_options
      --with-system
      --layout=system
      --prefix=${CMAKE_BINARY_DIR}/INSTALL
      --variant=release
  INSTALL_COMMAND ""
  INSTALL_DIR ${CMAKE_BINARY_DIR}/INSTALL
)

# get_cmake_property(_variableNames VARIABLES)
# foreach (_variableName ${_variableNames})
#     message(STATUS "${_variableName}=${${_variableName}}")
# endforeach()

ExternalProject_Add(
	xz

	URL http://tukaani.org/xz/xz-5.2.2.tar.gz
    URL_MD5 7cf6a8544a7dae8e8106fdf7addfa28c

    UPDATE_COMMAND ""
    
    CONFIGURE_COMMAND "./configure"
        --host=${TARGET_ARCH}
        --prefix=${CMAKE_BINARY_DIR}/INSTALL2
        --enable-shared=no
        --disable-xz
        --disable-xzdec
        --disable-lzmadec
        --disable-lzmainfo
        --disable-lzma-links
        --disable-scripts 
        --disable-doc

    BUILD_COMMAND make
    BUILD_IN_SOURCE ON

    INSTALL_COMMAND make install

    LOG_DOWNLOAD 1
    LOG_UPDATE 1
    LOG_CONFIGURE 1
    LOG_BUILD 1
    LOG_INSTALL 1
)

ExternalProject_Add(
	innoextract
    DEPENDS
        xz
        boost
	URL
   		https://github.com/dscharrer/innoextract/archive/1.6.zip 
	CMAKE_ARGS
        -DWITH_CONV=builtin
        -DUSE_LZMA=ON
        -DCMAKE_BUILD_TYPE=Release
        -DUSE_STATIC_LIBS=ON
        -DZLIB_USE_STATIC_LIBS=OFF
        -DBZip2_USE_STATIC_LIBS=OFF
        -DLZMA_ROOT="${CMAKE_BINARY_DIR}/INSTALL2"
        -DBOOST_ROOT=${CMAKE_BINARY_DIR}/INSTALL
        -DBOOST_INCLUDEDIR=${CMAKE_BINARY_DIR}/INSTALL/include
        -DBoost_USE_STATIC_LIBS=ON
	UPDATE_COMMAND
		""
	INSTALL_COMMAND
		""
)

# set(Boost_USE_STATIC_LIBS ON)
# set(BOOST_ROOT "${CMAKE_BINARY_DIR}/INSTALL")
# 
# find_package(Boost REQUIRED COMPONENTS
# 	iostreams
# 	filesystem
# 	date_time
# 	system
# 	program_options
# )
#check_link_library(Boost Boost_LIBRARIES)
#list(APPEND LIBRARIES ${Boost_LIBRARIES})
#link_directories(${Boost_LIBRARY_DIRS})
#message(FATAL "Boost_LIBRARIES: ${Boost_LIBRARIES}")
