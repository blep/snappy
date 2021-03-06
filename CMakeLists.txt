cmake_minimum_required (VERSION 2.8)

project (snappy)    
SET(SNAPPY_IDE_FOLDER "snappy")

include_directories(${CMAKE_CURRENT_SOURCE_DIR})

# /wd4722: constructor never returns, potential memory leak because of a sigleton pattern
# /wd4702: unreachable code because of singleton pattern
# /wd4800: bool cast performance warning
# /wd4244: '=' : conversion from 'snappy::uint64' to 'snappy::uint32', possible loss of data
# /wd4018: '>=' : signed/unsigned mismatch
# /wd4309: 'argument' : truncation of constant value
# /wd4722: 'snappy::LogMessageCrash::~LogMessageCrash' : destructor never returns, potential memory leak
# /wd4715: '`anonymous namespace'::file::SetContents' : not all control paths return a value
# /wd4005: 'ARRAYSIZE' : macro redefinition
IF(MSVC)
    ADD_DEFINITIONS(-D_CRT_SECURE_NO_WARNINGS) # /wd4996: 'fopen': This function or variable may be unsafe. Consider using fopen_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details.
    SET(LEVELDB_COMPILE_FLAGS "/D_CRT_SECURE_NO_WARNINGS /wd4800 /wd4722 /wd4702 /wd4018 /wd4244 /wd4309 /wd4722 /wd4715 /wd4005")
ELSE(MSVC)
    SET(LEVELDB_COMPILE_FLAGS "-Wno-sign-compare")
ENDIF(MSVC)

SET(SNAPPY_TEST_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/snappy-test.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/snappy_unittest.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/snappy-test.h
    )

SET(SNAPPY_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/snappy-c.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/snappy-sinksource.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/snappy-stubs-internal.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/snappy.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/snappy-c.h
    ${CMAKE_CURRENT_SOURCE_DIR}/snappy-internal.h
    ${CMAKE_CURRENT_SOURCE_DIR}/snappy-sinksource.h
    ${CMAKE_CURRENT_SOURCE_DIR}/snappy-stubs-internal.h
    ${CMAKE_CURRENT_SOURCE_DIR}/snappy-stubs-public.h
    ${CMAKE_CURRENT_SOURCE_DIR}/snappy.h)

add_library(snappy ${SNAPPY_FILES})

set_target_properties(snappy PROPERTIES 
    FOLDER ${SNAPPY_IDE_FOLDER} 
    COMPILE_FLAGS ${LEVELDB_COMPILE_FLAGS})

##################################### TESTS #######################################
add_definitions(-DHAVE_WINDOWS_H)
add_executable(snappy_test ${SNAPPY_TEST_FILES})
target_link_libraries(snappy_test snappy)
set_target_properties(snappy_test PROPERTIES 
    FOLDER ${SNAPPY_IDE_FOLDER} 
    COMPILE_FLAGS ${LEVELDB_COMPILE_FLAGS})
