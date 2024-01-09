cmake_minimum_required(VERSION 3.21)

project(
  CPRExportFailure
  LANGUAGES CXX
  VERSION 1.0.0)

#disable testing to build faster and avoid fetching even more dependencies for this example
set(ENABLE_TESTING False)
#force cpr to NOT use system CURL
unset(CPR_USE_SYSTEM_CURL)
include(FetchContent)

# Fetch cpr which is needed by unleash below
# as unleash tries to fetch it using find_package which relies on the exports being generated
FetchContent_Declare(
  cpr
  GIT_REPOSITORY https://github.com/libcpr/cpr.git
  GIT_TAG 1.10.3
  OVERRIDE_FIND_PACKAGE)

# Another dependency of unleash
FetchContent_Declare(
  nlohmann_json
  URL https://github.com/nlohmann/json/releases/download/v3.11.3/json.tar.xz
      OVERRIDE_FIND_PACKAGE)
FetchContent_MakeAvailable(nlohmann_json cpr)

# unleash relies on cpr 
FetchContent_Declare(
  unleash
  GIT_REPOSITORY https://github.com/bernedom/unleash-client-cpp.git
  GIT_TAG main
  OVERRIDE_FIND_PACKAGE EXCLUDE_FROM_ALL)

FetchContent_MakeAvailable(unleash)

add_executable(CPRExportFailure src/main.cpp)
target_link_libraries(CPRExportFailure PRIVATE unleash::unleash)
target_compile_features(CPRExportFailure PRIVATE cxx_std_20)

# install the project which triggers the error
# CMake Error: install(EXPORT "unleash_Targets" ...) includes target "unleash" which requires target "cpr" that is not in any export set.
install(TARGETS CPRExportFailure DESTINATION bin)
