# Set sources.
set(SOURCES 
  BuildSystemBindings.swift
  CoreBindings.swift)

# Link C API.
list(APPEND additional_args -I ${CMAKE_CURRENT_SOURCE_DIR}/../libllbuild/include -lllbuild)
list(APPEND additional_args -target x86_64-apple-macosx10.10)

# Add swift bindings target if swift compiler is present.
if (SWIFTC_FOUND)
  add_swift_module(libllbuildSwift llbuildSwift libllbuild "${SOURCES}" "${additional_args}")

  # Install libllbuild.
  install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/../libllbuild/include
    DESTINATION lib/swift/pm/llbuild
    COMPONENT libllbuildSwift
    FILES_MATCHING
    PATTERN "*.h"
    PATTERN "module.modulemap")
  
  # Install the swift headers.
  install(FILES 
      ${CMAKE_CURRENT_BINARY_DIR}/llbuildSwift.swiftmodule
      ${CMAKE_CURRENT_BINARY_DIR}/llbuildSwift.swiftdoc
    DESTINATION lib/swift/pm/llbuild/include
    COMPONENT libllbuildSwift)

  # Install the library.
  install(FILES "${LLBUILD_LIBRARY_OUTPUT_INTDIR}/libllbuildSwift.dylib"
    DESTINATION lib/swift/pm/llbuild
    COMPONENT libllbuildSwift)
  
  # Add install target.
  add_custom_target(install-libllbuildSwift
    DEPENDS libllbuildSwift
    COMMENT "Installing libllbuildSwift..."
    COMMAND "${CMAKE_COMMAND}"
      -DCMAKE_INSTALL_COMPONENT=libllbuildSwift
      -P "${CMAKE_BINARY_DIR}/cmake_install.cmake")
  
endif()
