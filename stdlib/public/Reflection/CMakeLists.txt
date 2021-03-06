set(swiftReflection_SOURCES
  MetadataSource.cpp
  TypeLowering.cpp
  TypeRef.cpp
  TypeRefBuilder.cpp
  "${SWIFT_SOURCE_DIR}/lib/Demangling/Context.cpp"
  "${SWIFT_SOURCE_DIR}/lib/Demangling/OldDemangler.cpp"
  "${SWIFT_SOURCE_DIR}/lib/Demangling/Demangler.cpp"
  "${SWIFT_SOURCE_DIR}/lib/Demangling/NodePrinter.cpp"
  "${SWIFT_SOURCE_DIR}/lib/Demangling/ManglingUtils.cpp"
  "${SWIFT_SOURCE_DIR}/lib/Demangling/Punycode.cpp"
  "${SWIFT_SOURCE_DIR}/lib/Demangling/Remangler.cpp"
  "${SWIFT_SOURCE_DIR}/lib/Demangling/TypeDecoder.cpp")

# When we're building with assertions, include the demangle node dumper to aid
# in debugging.
if (LLVM_ENABLE_ASSERTIONS)
  list(APPEND swiftReflection_SOURCES
    "${SWIFT_SOURCE_DIR}/lib/Demangling/NodeDumper.cpp")
endif(LLVM_ENABLE_ASSERTIONS)

add_swift_target_library(swiftReflection STATIC TARGET_LIBRARY
  ${swiftReflection_SOURCES}
  C_COMPILE_FLAGS ${SWIFT_RUNTIME_CXX_FLAGS}
  LINK_FLAGS ${SWIFT_RUNTIME_LINK_FLAGS}
  INSTALL_IN_COMPONENT dev)

# Build a specific version for the host with the host toolchain.  This is going
# to be used by tools (e.g. lldb)

if(NOT SWIFT_BUILD_RUNTIME_WITH_HOST_COMPILER)
  set(CMAKE_C_COMPILER ${HOST_CMAKE_C_COMPILER})
  set(CMAKE_CXX_COMPILER ${HOST_CMAKE_CXX_COMPILER})
endif()

add_swift_host_library(swiftReflection STATIC
  ${swiftReflection_SOURCES})
target_compile_options(swiftReflection PRIVATE
  ${SWIFT_RUNTIME_CXX_FLAGS})
set_property(TARGET swiftReflection
  APPEND_STRING PROPERTY LINK_FLAGS ${SWIFT_RUNTIME_LINK_FLAGS})

