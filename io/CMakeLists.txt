cmake_minimum_required (VERSION 3.3)
project(fortran2018io Fortran CXX)
enable_testing()

get_directory_property(hasParent PARENT_DIRECTORY)
if(NOT hasParent)
  include(../cmake/compilers.cmake)
endif()

set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_REQUIRED_FLAGS -std=c++17)
include(CheckCXXSymbolExists)
CHECK_CXX_SYMBOL_EXISTS(std::filesystem::path::preferred_separator filesystem cxx17)


add_executable(termio terminal_io.f90)
target_compile_options(termio PRIVATE ${FFLAGS})
target_link_libraries(termio ${FLIBS})
if(NOT WIN32) # FIXME doable via windows command
  add_test(NAME FortranTerminalIO COMMAND bash -c "./termio <<< 0")
endif()


add_executable(expanduser expanduser.f90)
target_compile_options(expanduser PRIVATE ${FFLAGS})
target_link_libraries(expanduser ${FLIBS})

add_executable(notermsize noenv.f90)
target_compile_options(notermsize PRIVATE ${FFLAGS})
target_link_libraries(notermsize ${FLIBS})
add_test(NAME NoTermSize COMMAND notermsize)

if(f08errorstop)
  add_executable(readonly fileopen-readonly.f90)
  target_compile_options(readonly PRIVATE ${FFLAGS})
  target_link_libraries(readonly ${FLIBS})
  add_test(NAME FortranReadOnly COMMAND readonly)

  add_executable(null devnull.f90)
  target_compile_options(null PRIVATE ${FFLAGS})
  target_link_libraries(null ${FLIBS})
  add_test(NAME FortranNullfile COMMAND null)
endif()

# --- C++17 filesystem ---

if(cxx17)
  add_executable(filesep_cpp filesep.cpp)
  set_property(TARGET filesep_cpp PROPERTY CXX_STANDARD 17)
endif()
