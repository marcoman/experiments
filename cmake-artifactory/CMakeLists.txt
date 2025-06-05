# CMakeLists.txt
cmake_minimum_required(VERSION 3.5)

# Set the project name
project(HelloWorld)

# Specify the C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)
set(CMAKE_BINARY_DIR "${CMAKE_SOURCE_DIR}/build")
set(CMAKE_CURRENT_BINARY_DIR "${CMAKE_BINARY_DIR}/artifacts")

# Print the source directory
message(STATUS "Source directory: ${CMAKE_SOURCE_DIR}")

# Add the executable
add_executable(hello src/main.cpp)

function(get_artifact ARTIFACT_PATH OUTPUT_PATH)
    # Environment variables for Artifactory details
    set(ARTIFACTORY_URL $ENV{ARTIFACTORY_URL})
    set(ARTIFACTORY_REPO $ENV{ARTIFACTORY_REPO})
    set(ARTIFACTORY_USER $ENV{ARTIFACTORY_USER})
    set(ARTIFACTORY_PASSWORD $ENV{ARTIFACTORY_PASSWORD}) # Or use an API key via ARTIFACTORY_API_KEY

    # Ensure required variables are set
    if(NOT ARTIFACTORY_URL OR NOT ARTIFACTORY_REPO)
        message(FATAL_ERROR "Missing required environment variables: ARTIFACTORY_URL or ARTIFACTORY_REPO")
    endif()

    # Construct the full artifact URL
    set(FULL_URL "${ARTIFACTORY_REPO}/${ARTIFACT_PATH}")

    # Execute JFrog CLI command to download the artifact
    execute_process(
        COMMAND jfrog rt download "${FULL_URL}" "${OUTPUT_PATH}"
        RESULT_VARIABLE RESULT_CODE
        OUTPUT_VARIABLE COMMAND_OUTPUT
        ERROR_VARIABLE ERROR_OUTPUT
    )

    # Check for errors
    if(NOT RESULT_CODE EQUAL 0)
        message(FATAL_ERROR "Failed to download artifact: ${ERROR_OUTPUT}")
    else()
        message(STATUS "Successfully downloaded artifact: ${COMMAND_OUTPUT}")
    endif()
endfunction()

message(STATUS "your current binary directory is: ${CMAKE_CURRENT_BINARY_DIR}")

# Get these packages from Artifactory
# marco-generic-dev-local/bom.1.4.cdx.json
# marco-generic-dev-local/Build_ohadz-ejs-gha-191-novex.cdx.json
# marco-generic-dev-local/Build_ohadz-npm-gha-186.cdx.json
# marco-generic-dev-local/curl-7.76.1.zip
get_artifact("bom.1.4.cdx.json" "${CMAKE_CURRENT_BINARY_DIR}/bom.1.4.cdx.json")
get_artifact("Build_ohadz-ejs-gha-191-novex.cdx.json" "${CMAKE_CURRENT_BINARY_DIR}/Build_ohadz-ejs-gha-191-novex.cdx.json")
get_artifact("Build_ohadz-npm-gha-186.cdx.json" "${CMAKE_CURRENT_BINARY_DIR}/Build_ohadz-npm-gha-186.cdx.json")
get_artifact("curl-7.76.1.zip" "${CMAKE_CURRENT_BINARY_DIR}/curl-7.76.1.zip")