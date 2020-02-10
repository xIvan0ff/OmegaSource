# disable "bin dir"
# we cannot use a single folder for output since some files have same name and they overwrite each other
# please use install instead
unset(CMAKE_RUNTIME_OUTPUT_DIRECTORY)

set(LUA_VERSION "luajit" CACHE STRING "Lua version to use")
set_property(CACHE LUA_VERSION PROPERTY STRINGS lua51 lua52 lua53 luajit)
MESSAGE(STATUS "Lua version: ${LUA_VERSION}")


# option(LUA_STATIC "link lua statically" ON)
set(LUA_STATIC OFF)
if (LUA_STATIC)
  MESSAGE(STATUS "Lua linking: static")
else()
  MESSAGE(STATUS "Lua linking: dynamic")
endif()

if (LUA_VERSION MATCHES "luajit")
  add_subdirectory(luajit)
else()
  add_subdirectory(lua)
endif()
add_subdirectory(compatibility)
