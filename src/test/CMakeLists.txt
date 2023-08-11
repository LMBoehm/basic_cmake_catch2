enable_testing()

add_executable(some_test test_some.cpp)
target_link_libraries(some_test PRIVATE Catch2::Catch2WithMain)

catch_discover_tests(some_test)
