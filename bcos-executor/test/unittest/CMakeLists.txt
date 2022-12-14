configure_file(${CMAKE_CURRENT_SOURCE_DIR}/libvm/WasmPath.h.in ${CMAKE_CURRENT_SOURCE_DIR}/libvm/WasmPath.h @ONLY)

file(GLOB_RECURSE SOURCES "*.cpp" "*.h")
# cmake settings
set(TEST_BINARY_NAME test-bcos-executor)

add_executable(${TEST_BINARY_NAME} ${SOURCES} libprecompiled/PrecompiledCallTest.cpp)
target_include_directories(${TEST_BINARY_NAME} PRIVATE ./ ../../src ../../include)

find_package(Boost REQUIRED unit_test_framework)
find_package(TBB CONFIG REQUIRED)

target_link_libraries(${TEST_BINARY_NAME} ${EXECUTOR_TARGET} ${CRYPTO_TARGET} ${PBPROTOCOL_TARGET} bcos-framework Boost::serialization Boost::unit_test_framework)
target_compile_options(${TEST_BINARY_NAME} PRIVATE -Wno-error -fno-var-tracking -Wno-unused-parameter)
add_test(NAME test-executor WORKING_DIRECTORY ${CMAKE_RUNTIME_OUTPUT_DIRECTORY} COMMAND ${TEST_BINARY_NAME})

# for code coverage
if (COVERAGE)
    include(Coverage)
    config_coverage("executor-coverage" "'/usr*' 'boost/**' 'deps/**'")
endif ()
