CMAKE_MINIMUM_REQUIRED(VERSION 3.8)
PROJECT(hello LANGUAGES C)

IF (NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  MESSAGE(FATAL_ERROR "
No build type selected. You need to pass --preset=<type> in order to configure CMake project.
")
ENDIF()

SET(CMAKE_C_STANDARD 99)
SET(CMAKE_C_STANDARD_REQUIRED ON)
SET(CMAKE_C_EXTENSIONS OFF)

OPTION(USE_SANITIZER "Enable to build with target sanitizer" "no sanitizer")

FUNCTION(ADD_EXECUTABLE_WITH_SANITIZER TARGET_NAME FILE_NAME)
  # Create the executable.
  ADD_EXECUTABLE(${TARGET_NAME} ${FILE_NAME})

  # Add basic compile flags.
  TARGET_COMPILE_OPTIONS(${TARGET_NAME} PRIVATE -Wall -Wextra -Wpedantic)

  # Setup sanitizer(s).
  IF (USE_SANITIZER STREQUAL "no sanitizer")
    MESSAGE(STATUS "No sanitizer enabled")
  ELSE()
    IF (USE_SANITIZER STREQUAL "Many")
    MESSAGE(STATUS "Enabling AddressSanitizer, LeakSanitizer and UndefinedBehaviorSanitizer")
      TARGET_COMPILE_OPTIONS(${TARGET_NAME} PUBLIC -fsanitize=address,leak,undefined)
      TARGET_LINK_OPTIONS(${TARGET_NAME} PUBLIC -fsanitize=address,leak,undefined)
    ELSEIF(USE_SANITIZER STREQUAL "Thread")
    MESSAGE(STATUS "Enabling ThreadSanitizer")
      TARGET_COMPILE_OPTIONS(${TARGET_NAME} PUBLIC -fsanitize=thread)
      TARGET_LINK_OPTIONS(${TARGET_NAME} PUBLIC -fsanitize=thread)
    ENDIF()
  ENDIF()
ENDFUNCTION()

ADD_EXECUTABLE_WITH_SANITIZER("hello" "hello.c")
