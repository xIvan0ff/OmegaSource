file(GLOB LOCAL_SOURCES
    lua-compat-5.3/c-api/*.c lua-compat-5.3/c-api/*.cpp lua-compat-5.3/c-api/*.h lua-compat-5.3/c-api/*.hpp
)

add_library(luacompat STATIC "${LOCAL_SOURCES}")
target_include_directories(luacompat PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/lua-compat-5.3/c-api")
target_link_libraries(luacompat lualib)