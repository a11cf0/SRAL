﻿# CMakeList.txt : CMake project for SRAL, include source and define
# project specific logic here.
#
cmake_minimum_required (VERSION 3.20)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")

# Enable Hot Reload for MSVC compilers if supported.
if (POLICY CMP0141)
  cmake_policy(SET CMP0141 NEW)
  set(CMAKE_MSVC_DEBUG_INFORMATION_FORMAT "$<IF:$<AND:$<C_COMPILER_ID:MSVC>,$<CXX_COMPILER_ID:MSVC>>,$<$<CONFIG:Debug,RelWithDebInfo>:EditAndContinue>,$<$<CONFIG:Debug,RelWithDebInfo>:ProgramDatabase>>")
endif()

project ("SRAL")
add_library(${PROJECT_NAME}_obj OBJECT 
  "Include/SRAL.h" "SRC/SRAL.cpp" "SRC/Engine.h" "SRC/Encoding.h" "SRC/Encoding.cpp"
  "SRC/NVDA.h" "SRC/NVDA.cpp" "SRC/SAPI.h" "SRC/SAPI.cpp"
  "Dep/blastspeak.h" "Dep/blastspeak.c" "Dep/fsapi.h" "Dep/fsapi.c"
  "SRC/Jaws.h" "SRC/Jaws.cpp" "SRC/SpeechDispatcher.h" "SRC/SpeechDispatcher.cpp" "SRC/UIA.cpp"
  "Dep/UIAProvider.h" "Dep/UIAProvider.cpp")
set_property(TARGET ${PROJECT_NAME}_obj
  PROPERTY PUBLIC_HEADER "Include/SRAL.h")
set_property(TARGET ${PROJECT_NAME}_obj
  PROPERTY POSITION_INDEPENDENT_CODE on)

add_library(${PROJECT_NAME} SHARED
  $<TARGET_OBJECTS:${PROJECT_NAME}_obj>)
add_library(${PROJECT_NAME}_static STATIC
  $<TARGET_OBJECTS:${PROJECT_NAME}_obj>)

install(TARGETS ${PROJECT_NAME}_obj
  PUBLIC_HEADER DESTINATION "include/${PROJECT_NAME}"
  INCLUDES DESTINATION "include/${PROJECT_NAME}")

install(TARGETS ${PROJECT_NAME}
  RUNTIME DESTINATION "bin"
  LIBRARY DESTINATION "lib"
  ARCHIVE DESTINATION "lib")
install(TARGETS ${PROJECT_NAME}_static DESTINATION "lib")

if (WIN32)
  target_link_libraries(${PROJECT_NAME} "uiautomationcore.lib" "winmm.lib")
else()
  set_source_files_properties(
    "SRC/Encoding.h" "SRC/Encoding.cpp" "SRC/NVDA.h" "SRC/NVDA.cpp" "SRC/SAPI.h" "SRC/SAPI.cpp"
    "Dep/blastspeak.h" "Dep/blastspeak.c" "Dep/fsapi.h" "Dep/fsapi.c"
    "SRC/Jaws.h" "SRC/Jaws.cpp" "SRC/UIA.cpp" "Dep/UIAProvider.h" "Dep/UIAProvider.cpp"
    PROPERTIES HEADER_FILE_ONLY TRUE)
endif()
