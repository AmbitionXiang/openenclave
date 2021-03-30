# Copyright (c) Open Enclave SDK contributors.
# Licensed under the MIT License.

# Using the default setting such that the library will be generated
# in a separated directory from the enclave binary. This is intended
# for the netagive test; i.e., the loader logic assumes the library is
# placed in the same directory as the enclave binary.
add_enclave_library(module_negative_path SHARED ../module/module.c)

maybe_build_using_clangw(module_negative_path)

# Use OE's default flags for enclaves
enclave_compile_options(
  module_negative_path
  PUBLIC
  -fPIC
  -nostdinc
  -fstack-protector-strong
  # Preserve frame-pointer in Release mode to enable oe_backtrace.
  -fno-omit-frame-pointer
  # Put each function or data in its own section.
  # This allows aggressively eliminating unused code.
  -ffunction-sections
  -fdata-sections
  # "The default without -fpic is 'initial-exec'; with -fpic the
  # default is 'global-dynamic'."
  # https://gcc.gnu.org/onlinedocs/gcc/Code-Gen-Options.html#Code-Gen-Options
  #
  # Enclaves are linked using -pie and therefore global-dynamic is
  # too conservative. Of the two efficient static thread-local
  # storage models, inital-exec and local-exec, we choose the most
  # optimal one.
  -ftls-model=local-exec
  # Disable builtin functions for enclaves, but only in our build.
  #
  # We do this to work-around compiler bugs (see #1429) due to our
  # redefinition of `memmove` to `oe_memmove` causing an undefined
  # symbol error when a built-in was inlined. However, we only do
  # this for our code as we don't want to force these flags on the
  # user. There are valid reasons for an end user to use built-ins.
  $<BUILD_INTERFACE:-fno-builtin-malloc
  -fno-builtin-calloc
  -fno-builtin>)

enclave_link_libraries(
  module_negative_path
  PRIVATE
  -nostdlib
  -nodefaultlibs
  -Wl,-z,noexecstack
  -Wl,-z,now
  -Wl,-gc-sections)
