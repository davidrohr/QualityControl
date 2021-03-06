# ---- Library ----

add_library(QcMID)

target_sources(QcMID PRIVATE src/RawQcCheck.cxx  src/RawQcTask.cxx )

target_include_directories(
  QcMID
  PUBLIC $<INSTALL_INTERFACE:include>
         $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/src)

target_link_libraries(QcMID PUBLIC QualityControl)

install(TARGETS QcMID
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})

add_root_dictionary(QcMID
                    HEADERS
		    include/MID/RawQcCheck.h
		    include/MID/RawQcTask.h
                    LINKDEF include/MID/LinkDef.h
                    BASENAME QcMID)

# ---- Test(s) ----

#set(TEST_SRCS test/testQcMID.cxx)

#foreach(test ${TEST_SRCS})
#  get_filename_component(test_name ${test} NAME)
#  string(REGEX REPLACE ".cxx" "" test_name ${test_name})

#  add_executable(${test_name} ${test})
#  target_link_libraries(${test_name}
#                        PRIVATE QcMID Boost::unit_test_framework)
#  add_test(NAME ${test_name} COMMAND ${test_name})
#  set_property(TARGET ${test_name}
#    PROPERTY RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/tests)
#  set_tests_properties(${test_name} PROPERTIES TIMEOUT 20)
#endforeach()

# ---- Executables ----

set(EXE_SRCS src/runMID.cxx)

set(EXE_NAMES o2-qc-run-mid)

list(LENGTH EXE_SRCS count)
math(EXPR count "${count}-1")
foreach(i RANGE ${count})
  list(GET EXE_SRCS ${i} src)
  list(GET EXE_NAMES ${i} name)
  add_executable(${name} ${src})
  target_link_libraries(${name} PRIVATE QualityControl CURL::libcurl O2::MIDWorkflow O2::DetectorsBase)
endforeach()

install(
  TARGETS ${EXE_NAMES}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)
