# This subdirectory verifies that the user level api compiles using
# only the publiced includes by compiling test files refering to that
# api. Goal is to use, and thus verify, the whole api
#
# There is no need to run the tests...

if (WITH_CXX)
  project(api CXX)
else()
  project(api C)
endif (WITH_CXX)

include_directories(${CGREEN_PUBLIC_INCLUDE_DIRS})

set(api_SRCS
  core_api.c
  suites_without_context_api.c
  suites_with_context_api.c
)

if (WITH_CXX)
  SET_SOURCE_FILES_PROPERTIES( ${api_SRCS} PROPERTIES LANGUAGE CXX )
endif (WITH_CXX)


set(CGREEN_API_TEST_LIBRARY
  cgreen_api
  CACHE INTERNAL "cgreen library of user level api verification source"
)

add_library(${CGREEN_API_TEST_LIBRARY} SHARED ${api_SRCS})

if (WITH_CXX)
   target_link_libraries(${CGREEN_API_TEST_LIBRARY} ${CGREEN_LIBRARY} ${CMAKE_THREAD_LIBS_INIT} ${CMAKE_CXX_IMPLICIT_LINK_LIBRARIES})
else (WITH_CXX)
   target_link_libraries(${CGREEN_API_TEST_LIBRARY} ${CGREEN_LIBRARY} ${CMAKE_THREAD_LIBS_INIT})
endif (WITH_CXX)

add_test(NAME api_compilation_test COMMAND ${CMAKE_SOURCE_DIR}/tools/compare_test_output_to_expected ${CMAKE_BINARY_DIR}/tools $<TARGET_FILE_NAME:${CGREEN_API_TEST_LIBRARY}> ${CGREEN_API_TEST_LIBRARY} ${CMAKE_CURRENT_SOURCE_DIR})
