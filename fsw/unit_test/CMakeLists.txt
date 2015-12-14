include(CTest)
find_program( MEMORYCHECK_COMMAND valgrind )
find_package(Threads)
set( MEMORYCHECK_COMMAND_OPTIONS "--trace-children=yes --leak-check=full" )

# Set include directories
include_directories(
	${CMAKE_CURRENT_SOURCE_DIR}
	${CFE_INC_DIRS}
	${OSAL_INC_DIRS}
	${UTF_INCS}
	${APP_INC_DIRS})

add_definitions(-DCFE_LINUX)

set(CMAKE_C_FLAGS "-g -O0 -Wformat=0 -Wno-pointer-to-int-cast -Wno-int-to-pointer-cast -fprofile-arcs -ftest-coverage -m32")

# Add the executables
add_executable(MM-UT 
		${CMAKE_CURRENT_SOURCE_DIR}/../../../cfs_lib/fsw/src/cfs_utils.c
		${CMAKE_CURRENT_SOURCE_DIR}/ut_mm_test_dumpinevent.c
		${CMAKE_CURRENT_SOURCE_DIR}/ut_mm_test_dumptofile.c
		${CMAKE_CURRENT_SOURCE_DIR}/ut_mm_test_fill.c
		${CMAKE_CURRENT_SOURCE_DIR}/ut_mm_test_loadfromfile.c
		${CMAKE_CURRENT_SOURCE_DIR}/ut_mm_test_loadwid.c
		${CMAKE_CURRENT_SOURCE_DIR}/ut_mm_test_peeks.c
		${CMAKE_CURRENT_SOURCE_DIR}/ut_mm_test_pokes.c
		${CMAKE_CURRENT_SOURCE_DIR}/ut_mm_test_symlookup.c
		${CMAKE_CURRENT_SOURCE_DIR}/ut_mm_test_symtbldump.c
		${CMAKE_CURRENT_SOURCE_DIR}/utf_test_mm.c
		${APP_SRC}
		${UTF_SRC})
target_link_libraries(MM-UT -fprofile-arcs gcov ${CMAKE_THREAD_LIBS_INIT})

# We need to both run the test and compare the as-run with the expected output
# with a diff command. 
add_test(NAME mm-ut
    COMMAND ${CMAKE_COMMAND}
    	-DTEST_PROG=$<TARGET_FILE:MM-UT>
    	-DEXPECTED_OUTPUT=${CMAKE_CURRENT_SOURCE_DIR}/mm_utf_test.out
    	-DACTUAL_OUTPUT=${CMAKE_CURRENT_BINARY_DIR}/mm_utf_test.out
    -P ${CMAKE_CURRENT_SOURCE_DIR}/runtest.cmake)

# Copy input files to build directory
file(MAKE_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/ram)
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/mm_utf_cmds.in ${CMAKE_CURRENT_BINARY_DIR} COPYONLY)
