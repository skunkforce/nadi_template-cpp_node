cmake_minimum_required(VERSION 3.27)

set(PROJECT_NAME "nadi_your-name_node")

# Set C++ standard to C++23
set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF) # Disable compiler-specific extensions

include(FetchContent)

if(NOT TARGET nlohmann_json::nlohmann_json)
    FetchContent_Declare(json URL https://github.com/nlohmann/json/releases/download/v3.11.3/json.tar.xz)
    set(JSON_BuildTests OFF CACHE INTERNAL "")
    FetchContent_MakeAvailable(json)
endif()

if(NOT TARGET nadicpp::nadicpp)
    add_subdirectory(nadicpp)
endif()


# Define the DLL target
add_library(${PROJECT_NAME} SHARED
    src/main.cpp
)

# Platform-specific settings
if(WIN32)
    # On Windows, ensure DLL exports symbols correctly
    set_target_properties(${PROJECT_NAME} PROPERTIES
        WINDOWS_EXPORT_ALL_SYMBOLS OFF # We control exports manually
    )
endif()

target_include_directories(${PROJECT_NAME}
        PUBLIC
            $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
            $<INSTALL_INTERFACE:include>
    )

target_link_libraries(${PROJECT_NAME} PRIVATE 
    nlohmann_json::nlohmann_json 
    nadi::nadi 
    nadicpp::nadicpp)


    
