project ( lua C )

set(LUA_SOURCE_FOLDER "${CMAKE_CURRENT_SOURCE_DIR}/LuaJIT-2.0.5")
set(LUA_BIN_FOLDER "${CMAKE_CURRENT_BINARY_DIR}/LuaJIT-2.0.5")
file(COPY ${LUA_SOURCE_FOLDER} DESTINATION ${CMAKE_CURRENT_BINARY_DIR})

# This method to add externally compiled library was found at https://stackoverflow.com/questions/31274577/custom-target-as-a-target-library-in-cmake
if (WIN32)
    # how to build the result of the library
    add_custom_command(
      OUTPUT ${LUA_BIN_FOLDER}/src/lua51.dll ${LUA_BIN_FOLDER}/src/lua51.lib ${LUA_BIN_FOLDER}/src/luajit.exe
      WORKING_DIRECTORY ${LUA_BIN_FOLDER}/src
      # COMMAND echo luajit built on platform $(Platform)
      # COMMAND cd $(VSInstallDir)/VC
      # COMMAND if \"$(Platform)\"==\"Win32\" echo \"luajit building $(Platform)\" & call $(VSInstallDir)/VC/vcvarsall.bat $(Platform) & call msvcbuild.bat
      # COMMAND if \"$(Platform)\"==\"Win32\" echo \"luajit building 64bit\" & call $(VSInstallDir)/VC/vcvarsall.bat x64
      # COMMAND if \"$(Platform)\"==\"Win64\" echo luajit building 64bit
      # COMMAND if \"$(Platform)\"==\"Win64\" call $(VSInstallDir)/VC/vcvarsall.bat x64
      # COMMAND cd ${LUA_BIN_FOLDER}/src
      COMMAND call msvcbuild.bat
      # COMMAND ${CMAKE_COMMAND} -E copy ${LUA_BIN_FOLDER}/src/lua51.dll ${CMAKE_BINARY_DIR}/$(ConfigurationName)/
      # COMMAND ${CMAKE_COMMAND} -E copy ${LUA_BIN_FOLDER}/src/lua51.lib ${CMAKE_BINARY_DIR}/$(ConfigurationName)/
      # COMMAND ${CMAKE_COMMAND} -E copy ${LUA_BIN_FOLDER}/src/luajit.exe ${CMAKE_BINARY_DIR}/$(ConfigurationName)/
    )
    add_custom_target(luajit_target DEPENDS ${LUA_BIN_FOLDER}/src/lua51.lib)

    # create an library target out of the library compilation result
    add_library(lualib SHARED IMPORTED GLOBAL)
    add_dependencies(lualib luajit_target)

    # specify where the library is and where to find the headers
    set_target_properties(lualib
    PROPERTIES
    IMPORTED_LOCATION ${LUA_BIN_FOLDER}/src/lua51.dll
    IMPORTED_IMPLIB ${LUA_BIN_FOLDER}/src/lua51.lib
    INTERFACE_INCLUDE_DIRECTORIES "${LUA_SOURCE_FOLDER}/src"
    )

    # install generated files
    install(FILES ${LUA_BIN_FOLDER}/src/lua51.dll ${LUA_BIN_FOLDER}/src/lua51.lib ${LUA_BIN_FOLDER}/src/luajit.exe DESTINATION "${CMAKE_INSTALL_PREFIX}")
    install(DIRECTORY ${LUA_BIN_FOLDER}/src/jit DESTINATION "${CMAKE_INSTALL_PREFIX}/lua")
endif()

if (UNIX OR APPLE)
    #option(LUA_USR "Use /usr/local/ as lua library location" OFF)
    #if (LUA_USR)
    #  set(LUA_INSTALL_PATH "/usr/local")
    #else()
      set(LUA_INSTALL_PATH "${CMAKE_CURRENT_BINARY_DIR}/BIN")
    #endif()

    if (LUA_STATIC)
      set(LUAJIT_LIB_PATH "${LUA_INSTALL_PATH}/lib/libluajit-5.1.a")
    else()
      set(LUAJIT_LIB_PATH "${LUA_INSTALL_PATH}/lib/libluajit-5.1.so.2.0.5")
    endif()

    # how to build the result of the library
    # if (LUA_USR)
    #   add_custom_command(
    #     OUTPUT ${LUAJIT_LIB_PATH}
    #     COMMAND $(MAKE) -C ${LUA_BIN_FOLDER}
    #     COMMAND $(MAKE) -C ${LUA_BIN_FOLDER} install
    #   )
    # else ()
      add_custom_command(
        OUTPUT ${LUAJIT_LIB_PATH}
        # COMMAND $(MAKE) -C ${LUA_BIN_FOLDER} PREFIX=${LUA_INSTALL_PATH}
        COMMAND $(MAKE) -C ${LUA_BIN_FOLDER} install PREFIX=${LUA_INSTALL_PATH}
      )
    # endif()
    add_custom_target(luajit_target DEPENDS ${LUAJIT_LIB_PATH})

    # create an library target out of the library compilation result
    add_library(lualib SHARED IMPORTED GLOBAL)
    add_dependencies(lualib luajit_target)

    # specify where the library is and where to find the headers
    set_target_properties(lualib
    PROPERTIES
    # IMPORTED_LOCATION ${LUAJIT_LIB_PATH} # cmake bullshit. spent days figuring this and turns out set_target_properties does squat shit while set_property works fine.
    INTERFACE_INCLUDE_DIRECTORIES "${LUA_SOURCE_FOLDER}/src"
    )
    set_property(TARGET lualib PROPERTY IMPORTED_LOCATION ${LUAJIT_LIB_PATH})

    # install generated files
    # note that the / at the end means that we copy folder contents, not the folder itself
    install(DIRECTORY ${LUA_INSTALL_PATH}/ DESTINATION ${CMAKE_INSTALL_PREFIX})
endif()

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DLUA_VERSION_NUM=501")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DLUA_VERSION_NUM=501")
