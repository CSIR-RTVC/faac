cmake_minimum_required(VERSION 3.11)
project(libfaac VERSION 1.0.0 LANGUAGES C CXX)

#Make sure that custom modules like FindRapidJSON are found
list(INSERT CMAKE_MODULE_PATH 0 ${CMAKE_SOURCE_DIR}/cmake)

SET( FAAC_HDRS
    ./include/faac.h
    ./include/faaccfg.h
    ./libfaac/aacquant.h
    ./libfaac/backpred.h
    ./libfaac/bitstream.h
    ./libfaac/channels.h
    ./libfaac/coder.h
    ./libfaac/fft.h
    ./libfaac/filtbank.h
    ./libfaac/frame.h
    ./libfaac/huffman.h
    ./libfaac/hufftab.h
    ./libfaac/ltp.h
    ./libfaac/midside.h
    ./libfaac/psych.h
    ./libfaac/tns.h
    ./libfaac/util.h
    ./libfaac/version.h
    ./libfaac/kiss_fft/_kiss_fft_guts.h
    ./libfaac/kiss_fft/kiss_fft.h
    ./libfaac/kiss_fft/kiss_fftr.h
)

SET( FAAC_SRCS
    ./libfaac/aacquant.c
    ./libfaac/backpred.c
    ./libfaac/bitstream.c
    ./libfaac/channels.c
    ./libfaac/fft.c
    ./libfaac/filtbank.c
    ./libfaac/frame.c
    ./libfaac/huffman.c
    ./libfaac/ltp.c
    ./libfaac/midside.c
    ./libfaac/psychkni.c
    ./libfaac/tns.c
    ./libfaac/util.c
    ./libfaac/kiss_fft/kiss_fft.c
    ./libfaac/kiss_fft/kiss_fftr.c
)

# interface library for now
add_library(libfaac STATIC
    ${FAAC_SRCS} 
	${FAAC_HDRS}
)


#Set target properties
target_include_directories(libfaac
    PUBLIC
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include/>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/libfaad
)

# get rid of some compiler warnings
TARGET_COMPILE_DEFINITIONS(libfaac 
    PRIVATE 
        "-D_CRT_SECURE_NO_WARNINGS" "-D_LIB" "-DUSE_MATH_DEFINES"
		)

TARGET_COMPILE_FEATURES(libfaac PRIVATE cxx_auto_type)
TARGET_COMPILE_OPTIONS(libfaac PRIVATE $<$<CXX_COMPILER_ID:GNU>:-Wall>)

target_link_libraries(libfaac
    PUBLIC
    PRIVATE
)

##############################################
# Installation instructions

include(GNUInstallDirs)
set(INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/libfaac)

install(TARGETS libfaac
    EXPORT libfaac-targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

#Export the targets to a script
install(EXPORT libfaac-targets
  FILE
    libfaacTargets.cmake
  NAMESPACE
    libfaac::
  DESTINATION
    ${INSTALL_CONFIGDIR}
)

#Create a ConfigVersion.cmake file
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/libfaacConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(${CMAKE_CURRENT_LIST_DIR}/cmake/libfaacConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/libfaacConfig.cmake
    INSTALL_DESTINATION ${INSTALL_CONFIGDIR}
)

#Install the config, configversion and custom find modules
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/libfaacConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/libfaacConfigVersion.cmake
    DESTINATION ${INSTALL_CONFIGDIR}
)

##############################################
export(EXPORT libfaac-targets FILE ${CMAKE_CURRENT_BINARY_DIR}/libfaacTargets.cmake NAMESPACE libfaac::)

#Register package in user's package registry
export(PACKAGE libfaac)
