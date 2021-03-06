find_package(nlohmann_json 3.5.0 CONFIG QUIET)

if(TARGET nlohmann_json::nlhomann_json)
  get_target_property(
    _loc
    nlohmann_json::nlohmann_json
    INTERFACE_INCLUDE_DIRECTORIES
    )
  message(STATUS "Found nlohmann_json: ${_loc} (found version ${nlohmann_json_VERSION})")
  add_library(nlohmann_json_external INTERFACE)  # dummy
else()
  message(STATUS "Suitable nlohmann_json could not be located. Downloading and building!")

  include(ExternalProject)
  ExternalProject_Add(nlohmann_json_external
    GIT_REPOSITORY
      https://github.com/nlohmann/json
    GIT_TAG
      v3.5.0
    UPDATE_COMMAND ""
    CMAKE_ARGS
      -DCMAKE_INSTALL_PREFIX=${STAGED_INSTALL_PREFIX}
      -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
      -DCMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER}
      -DCMAKE_CXX_STANDARD=${CMAKE_CXX_STANDARD}
      -DJSON_BuildTests=OFF
    LOG_DOWNLOAD 1
    LOG_UPDATE 1
    LOG_CONFIGURE 1
    LOG_BUILD 1
    LOG_INSTALL 1
    )
  set(nlohmann_json_DIR ${STAGED_INSTALL_PREFIX}/lib/cmake/nlohmann_json CACHE PATH "Path to internally built nlohmann_jsonConfig.cmake" FORCE)
endif()
