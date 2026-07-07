cmake_minimum_required(VERSION 3.24 FATAL_ERROR)

project(sajson)

add_library(sajson INTERFACE)
add_library(sajson::sajson ALIAS sajson)

target_sources(sajson
    INTERFACE FILE_SET HEADERS
    BASE_DIRS
        include
    FILES
        sajson.h
        sajson_ostream.h
)

if(PROJECT_IS_TOP_LEVEL)
    # tests
    enable_testing()

    add_library(unittestpp STATIC
        third-party/UnitTest++/src/AssertException.cpp
        third-party/UnitTest++/src/Checks.cpp
        third-party/UnitTest++/src/CurrentTest.cpp
        third-party/UnitTest++/src/DeferredTestReporter.cpp
        third-party/UnitTest++/src/DeferredTestResult.cpp
        third-party/UnitTest++/src/MemoryOutStream.cpp
        third-party/UnitTest++/src/ReportAssert.cpp
        third-party/UnitTest++/src/Test.cpp
        third-party/UnitTest++/src/TestDetails.cpp
        third-party/UnitTest++/src/TestList.cpp
        third-party/UnitTest++/src/TestReporter.cpp
        third-party/UnitTest++/src/TestReporterStdout.cpp
        third-party/UnitTest++/src/TestResults.cpp
        third-party/UnitTest++/src/TestRunner.cpp
        third-party/UnitTest++/src/TimeConstraint.cpp
        third-party/UnitTest++/src/XmlTestReporter.cpp
    )
    if(WIN32)
        target_sources(unittestpp PRIVATE
            third-party/UnitTest++/src/Win32/TimeHelpers.cpp
        )
    else()
        target_sources(unittestpp PRIVATE
            third-party/UnitTest++/src/Posix/SignalTranslator.cpp
            third-party/UnitTest++/src/Posix/TimeHelpers.cpp
        )
    endif()
    target_include_directories(unittestpp INTERFACE third-party/UnitTest++/src)

    add_executable(sajson-test tests/test.cpp tests/test_no_stl.cpp)
    target_link_libraries(sajson-test PRIVATE sajson::sajson unittestpp)
    add_test(NAME test COMMAND sajson-test)

    add_executable(sajson-test-unsorted tests/test.cpp tests/test_no_stl.cpp)
    target_link_libraries(sajson-test-unsorted PRIVATE sajson::sajson unittestpp)
    target_compile_definitions(sajson-test-unsorted PRIVATE SAJSON_UNSORTED_OBJECT_KEYS)
    add_test(NAME test-unsorted COMMAND sajson-test-unsorted)

    # benchmarks
    add_executable(sajson-bench benchmark/benchmark.cpp)
    target_link_libraries(sajson-bench PRIVATE sajson::sajson)

    # examples
    add_executable(sajson-example-main example/main.cpp)
    target_link_libraries(sajson-example-main PRIVATE sajson::sajson)
    add_executable(sajson-example-zero-alloc example/zero-allocation.cpp)
    target_link_libraries(sajson-example-zero-alloc PRIVATE sajson::sajson)
endif()
