add_library(luacompat STATIC "${CMAKE_CURRENT_SOURCE_DIR}/lua-compat-5.3/c-api/compat-5.3.c")
target_include_directories(luacompat PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/lua-compat-5.3/c-api")
target_link_libraries(luacompat lualib)

add_library(compat53_string SHARED "${CMAKE_CURRENT_SOURCE_DIR}/lua-compat-5.3/lstrlib.c")
set_target_properties(compat53_string PROPERTIES OUTPUT_NAME "string")
target_link_libraries(compat53_string luacompat)
target_include_directories(compat53_string PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/lua-compat-5.3")

add_library(compat53_table SHARED "${CMAKE_CURRENT_SOURCE_DIR}/lua-compat-5.3/ltablib.c")
set_target_properties(compat53_table PROPERTIES OUTPUT_NAME "table")
target_link_libraries(compat53_table luacompat)
target_include_directories(compat53_table PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/lua-compat-5.3")

add_library(compat53_utf8 SHARED "${CMAKE_CURRENT_SOURCE_DIR}/lua-compat-5.3/lutf8lib.c")
set_target_properties(compat53_utf8 PROPERTIES OUTPUT_NAME "utf8")
target_link_libraries(compat53_utf8 luacompat)
target_include_directories(compat53_utf8 PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/lua-compat-5.3")

if (APPLE OR UNIX)
  install(DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/lua-compat-5.3/compat53" DESTINATION "${CMAKE_INSTALL_PREFIX}/bin/lua_libs")
  install(TARGETS compat53_string compat53_table compat53_utf8 DESTINATION "${CMAKE_INSTALL_PREFIX}/bin/lua_libs")
endif()
if (WIN32)
  set_property(TARGET compat53_string compat53_table compat53_utf8 PROPERTY WINDOWS_EXPORT_ALL_SYMBOLS YES)
  install(DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/lua-compat-5.3/compat53" DESTINATION "${CMAKE_INSTALL_PREFIX}/lua_libs")
  install(TARGETS compat53_string compat53_table compat53_utf8 DESTINATION "${CMAKE_INSTALL_PREFIX}/lua_libs/compat53")
  install(
    FILES $<TARGET_PDB_FILE:compat53_string> $<TARGET_PDB_FILE:compat53_table> $<TARGET_PDB_FILE:compat53_utf8>
    DESTINATION "${CMAKE_INSTALL_PREFIX}/lua_libs/compat53"
    OPTIONAL
  )
endif()
