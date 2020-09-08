#
# Copyright(c) 2006 to 2019 ADLINK Technology Limited and others
#
# This program and the accompanying materials are made available under the
# terms of the Eclipse Public License v. 2.0 which is available at
# http://www.eclipse.org/legal/epl-2.0, or the Eclipse Distribution License
# v. 1.0 which is available at
# http://www.eclipse.org/org/documents/edl-v10.php.
#
# SPDX-License-Identifier: EPL-2.0 OR BSD-3-Clause
#
include(CUnit)

set(idlcxx_test_sources
    "cpp11Backend.c"
    "idl_ostream.c"
    "streamer_generator.c")

add_cunit_executable(cunit_idlcxx ${idlcxx_test_sources})
target_link_libraries(cunit_idlcxx PRIVATE idlcxx)


# Add location of CycloneDDS::idl to LD_LIBRARY_PATH, DYLD_LIBRARY_PATH, PATH
get_target_property(lib CycloneDDS::idl LOCATION)
get_filename_component(libdir "${lib}" PATH)
file(TO_NATIVE_PATH "${libdir}" libdir)

get_target_property(cunit_lib CUnit LOCATION)
get_filename_component(cunit_libdir "${cunit_lib}" PATH)
file(TO_NATIVE_PATH "${cunit_libdir}" cunit_libdir)
message(STATUS "LOCATION: ${cunit_lib}")
message(STATUS "LOCATION: ${cunit_libdir}")

if(WIN32)
  set(var "PATH")
  string(REPLACE ";" "\\;" paths "${libdir};${cunit_libdir};$ENV{${var}}")
elseif(APPLE)
  set(var "DYLD_LIBRARY_PATH")
  set(paths "${libdir}:${cunit_libdir}:$ENV{${var}}")
else()
  set(var "LD_LIBRARY_PATH")
  set(paths "${libdir}:${cunit_libdir}:$ENV{${var}}")
endif()


get_property(tests DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}" PROPERTY TESTS)
foreach(test ${tests})
  set_property(TEST ${test} APPEND PROPERTY ENVIRONMENT "${var}=${paths}")
endforeach()
