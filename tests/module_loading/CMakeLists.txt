# Copyright (c) Open Enclave SDK contributors.
# Licensed under the MIT License.

add_subdirectory(host)

if (BUILD_ENCLAVES)
  add_subdirectory(module)
  add_subdirectory(module_negative_path)
  add_subdirectory(enc)
endif ()

add_enclave_test(tests/module_loading module_loading_host module_loading_enc)

add_enclave_test(tests/module_loading_negative_path module_loading_host
                 module_loading_negative_path_enc)
set_enclave_tests_properties(
  tests/module_loading_negative_path
  PROPERTIES
  PASS_REGULAR_EXPRESSION
  "Failed to locate the module. Please place the module under the same directory as the enclave binary."
)

add_enclave_test(tests/module_loading_negative_rpath module_loading_host
                 module_loading_negative_rpath_enc)
set_enclave_tests_properties(
  tests/module_loading_negative_rpath PROPERTIES PASS_REGULAR_EXPRESSION
  "RPATH or RUNPATH should not be used in the enclave binary")

add_enclave_test(tests/module_loading_negative_runpath module_loading_host
                 module_loading_negative_rpath_enc)
set_enclave_tests_properties(
  tests/module_loading_negative_runpath PROPERTIES PASS_REGULAR_EXPRESSION
  "RPATH or RUNPATH should not be used in the enclave binary")
