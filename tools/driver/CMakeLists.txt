add_swift_host_tool(swift
  api_notes.cpp
  driver.cpp
  autolink_extract_main.cpp
  modulewrap_main.cpp
  swift_format_main.cpp
  LINK_LIBRARIES
    swiftDriver
    swiftFrontendTool
  LLVM_COMPONENT_DEPENDS
    DebugInfoCodeView
  SWIFT_COMPONENT compiler
)

if(HAVE_UNICODE_LIBEDIT)
  target_link_libraries(swift edit)
endif()

add_custom_command(TARGET swift POST_BUILD
    COMMAND "${CMAKE_COMMAND}" "-E" "create_symlink" "swift" "swiftc"
    WORKING_DIRECTORY "${SWIFT_RUNTIME_OUTPUT_INTDIR}")

add_custom_command(TARGET swift POST_BUILD
    COMMAND "${CMAKE_COMMAND}" "-E" "create_symlink" "swift" "swift-autolink-extract"
    WORKING_DIRECTORY "${SWIFT_RUNTIME_OUTPUT_INTDIR}")

add_custom_command(TARGET swift POST_BUILD
    COMMAND "${CMAKE_COMMAND}" "-E" "create_symlink" "swift" "swift-format"
    WORKING_DIRECTORY "${SWIFT_RUNTIME_OUTPUT_INTDIR}")

# If building as part of clang, make sure the headers are installed.
if(NOT SWIFT_BUILT_STANDALONE)
  add_dependencies(swift clang-headers)
endif()

swift_install_in_component(compiler
    FILES "${SWIFT_RUNTIME_OUTPUT_INTDIR}/swiftc"
    DESTINATION "bin")
swift_install_in_component(autolink-driver
    FILES "${SWIFT_RUNTIME_OUTPUT_INTDIR}/swift-autolink-extract"
    DESTINATION "bin")
swift_install_in_component(editor-integration
    FILES "${SWIFT_RUNTIME_OUTPUT_INTDIR}/swift-format"
    DESTINATION "bin")
