cmake_minimum_required(VERSION 3.10)
project(o1agent CXX)

# Temukan dependency CURL
find_package(CURL REQUIRED)

# Temukan dependency json-c via pkg-config
find_package(PkgConfig REQUIRED)
pkg_check_modules(JSONC REQUIRED json-c)

# Tambahkan semua file sumber .cpp ke library
file(GLOB_RECURSE SOURCES 
    "o1/*.cpp"
    "o1/ves/*.cpp"
)

add_library(o1agent STATIC ${SOURCES})

# Tambahkan direktori header
target_include_directories(o1agent PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/o1>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/o1/ves>
    $<INSTALL_INTERFACE:include>
    ${JSONC_INCLUDE_DIRS}
)

# Link ke library eksternal
target_link_libraries(o1agent PUBLIC CURL::libcurl ${JSONC_LIBRARIES})

# Install library
install(TARGETS o1agent
        EXPORT o1agentTargets
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin)

# Install header files
install(DIRECTORY o1/
        DESTINATION include/o1
        FILES_MATCHING PATTERN "*.h" PATTERN "*.hpp")
