# Set local variables
set(APPTARGET mm)

# Set source files
set(APP_SRC 
	${CMAKE_CURRENT_SOURCE_DIR}/../src/mm_app.c
	${CMAKE_CURRENT_SOURCE_DIR}/../src/mm_dump.c 
	${CMAKE_CURRENT_SOURCE_DIR}/../src/mm_load.c
	${CMAKE_CURRENT_SOURCE_DIR}/../src/mm_mem16.c
	${CMAKE_CURRENT_SOURCE_DIR}/../src/mm_mem32.c
	${CMAKE_CURRENT_SOURCE_DIR}/../src/mm_mem8.c
	${CMAKE_CURRENT_SOURCE_DIR}/../src/mm_utils.c)
	
# Set include paths
set(APP_INC_DIRS 
	${CMAKE_CURRENT_SOURCE_DIR}/../../../inc
	${CMAKE_CURRENT_SOURCE_DIR}/../src/)
include_directories(${APP_INC_DIRS})
include_directories(${CFE_INC_DIRS})
include_directories(${OSAL_INC_DIRS})
include_directories(${PSP_INC_DIRS})
	
# Build application
add_app(${APP_FILE} SHARED ${APP_SRC})

add_subdirectory(../unit_test unit_test)

# Generate documentation
find_package(Doxygen)
if(DOXYGEN_FOUND)
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/user_doxy.in ${CMAKE_CURRENT_BINARY_DIR}/user_doxy @ONLY)
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/detail_doxy.in ${CMAKE_CURRENT_BINARY_DIR}/detail_doxy @ONLY)
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/codewalk_doxy.in ${CMAKE_CURRENT_BINARY_DIR}/codewalk_doxy @ONLY)
    add_custom_target(mm-docs
        COMMENT "Generating API documentation with Doxygen" VERBATIM
    	COMMAND mkdir -p ${CMAKE_BINARY_DIR}/docs/users_guide/html/${APPTARGET}/
    	COMMAND mkdir -p ${CMAKE_BINARY_DIR}/docs/users_guide/latex/${APPTARGET}/
    	COMMAND mkdir -p ${CMAKE_BINARY_DIR}/docs/detailed_design/html/${APPTARGET}/
    	COMMAND mkdir -p ${CMAKE_BINARY_DIR}/docs/detailed_design/latex/${APPTARGET}/
    	COMMAND mkdir -p ${CMAKE_BINARY_DIR}/docs/cwt/html/${APPTARGET}/
    	COMMAND mkdir -p ${CMAKE_BINARY_DIR}/docs/cwt/latex/${APPTARGET}/
        COMMAND ${DOXYGEN_EXECUTABLE} ${CMAKE_CURRENT_BINARY_DIR}/user_doxy
        COMMAND ${DOXYGEN_EXECUTABLE} ${CMAKE_CURRENT_BINARY_DIR}/detail_doxy
        COMMAND ${DOXYGEN_EXECUTABLE} ${CMAKE_CURRENT_BINARY_DIR}/codewalk_doxy
        #COMMAND make -C ${CMAKE_BINARY_DIR}/docs/users_guide/latex/${APPTARGET}
        #COMMAND make -C ${CMAKE_BINARY_DIR}/docs/detailed_design/latex/${APPTARGET}
        #COMMAND make -C ${CMAKE_BINARY_DIR}/docs/cwt/latex/${APPTARGET}
        WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/
    )
endif(DOXYGEN_FOUND)
