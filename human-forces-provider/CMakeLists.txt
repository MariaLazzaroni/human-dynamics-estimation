cmake_minimum_required(VERSION 3.0)

project(human-forces-provider CXX)

## Find packages
find_package(YARP REQUIRED)

# Generate thrift files  
yarp_idl_to_dir("${CMAKE_CURRENT_SOURCE_DIR}/thrifts/Force6D.thrift" "${CMAKE_CURRENT_BINARY_DIR}/autogenerated" THRIFT_SOURCES THRIFT_HEADERS THRIFT_INCLUDE_DIRS)

add_library(human-forces-provider-data ${THRIFT_SOURCES} ${THRIFT_HEADERS})
target_include_directories(human-forces-provider-data SYSTEM PUBLIC ${THRIFT_INCLUDE_DIRS} ${YARP_INCLUDE_DIRS})
target_link_libraries(human-forces-provider-data YARP::YARP_init)
target_link_libraries(human-forces-provider-data YARP::YARP_OS)

set(SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/src/main.cpp 
            ${CMAKE_CURRENT_SOURCE_DIR}/src/HumanForcesProvider.cpp
            ${CMAKE_CURRENT_SOURCE_DIR}/src/ForceReader.cpp
            ${CMAKE_CURRENT_SOURCE_DIR}/src/AbstractForceReader.cpp
            ${CMAKE_CURRENT_SOURCE_DIR}/src/FTForceReader.cpp
            ${CMAKE_CURRENT_SOURCE_DIR}/src/PortForceReader.cpp
            ${CMAKE_CURRENT_SOURCE_DIR}/src/FrameTransformer.cpp
            ${CMAKE_CURRENT_SOURCE_DIR}/src/GenericFrameTransformer.cpp
            ${CMAKE_CURRENT_SOURCE_DIR}/src/RobotFrameTransformer.cpp
)

set(HEADERS ${CMAKE_CURRENT_SOURCE_DIR}/include/HumanForcesProvider.h
            ${CMAKE_CURRENT_SOURCE_DIR}/include/ForceReader.h
            ${CMAKE_CURRENT_SOURCE_DIR}/include/AbstractForceReader.h
            ${CMAKE_CURRENT_SOURCE_DIR}/include/FTForceReader.h
            ${CMAKE_CURRENT_SOURCE_DIR}/include/PortForceReader.h
            ${CMAKE_CURRENT_SOURCE_DIR}/include/FrameTransformer.h                       
            ${CMAKE_CURRENT_SOURCE_DIR}/include/GenericFrameTransformer.h
            ${CMAKE_CURRENT_SOURCE_DIR}/include/RobotFrameTransformer.h
)

## Declare a C++ executable
add_executable(${PROJECT_NAME} ${SOURCES} ${HEADERS})

## Include directories
target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include)

## Specify targets for the linkage
target_link_libraries(${PROJECT_NAME} human-forces-provider-data 
                                      human-state-provider-data
                                      human-rosmsgs
                                      YARP::YARP_OS
                                      YARP::YARP_init
                                      YARP::YARP_sig
                                      YARP::YARP_dev
                                      iDynTree::idyntree-core
                                      iDynTree::idyntree-high-level
                                      iDynTree::idyntree-yarp)

add_warnings_configuration_to_target(TARGETS ${PROJECT_NAME} PRIVATE)

target_include_directories(${PROJECT_NAME} SYSTEM PRIVATE ${EIGEN3_INCLUDE_DIR})

yarp_install(FILES app/conf/human-forces-provider.ini DESTINATION ${YARP_CONTEXTS_INSTALL_DIR}/human-dynamic-estimation)

