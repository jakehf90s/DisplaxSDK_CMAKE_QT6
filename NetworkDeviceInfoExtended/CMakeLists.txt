cmake_minimum_required(VERSION 3.16)

project(DisplaxSDKCMake LANGUAGES CXX)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# -----------------------------------------
# Qt modules
# -----------------------------------------
find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Core Network Qml Quick QuickControls2)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Core Network Qml Quick QuickControls2)

# -----------------------------------------
# Source files and QML resources
# -----------------------------------------
set(SOURCES
    main.cpp
    qml.qrc
)

# -----------------------------------------
# Add executable
# -----------------------------------------
add_executable(${PROJECT_NAME} ${SOURCES})

# -----------------------------------------
# Include & Link with Qt
# -----------------------------------------
target_link_libraries(${PROJECT_NAME} PRIVATE
    Qt${QT_VERSION_MAJOR}::Core
    Qt${QT_VERSION_MAJOR}::Network
    Qt${QT_VERSION_MAJOR}::Qml
    Qt${QT_VERSION_MAJOR}::Quick
    Qt${QT_VERSION_MAJOR}::QuickControls2
)

# -----------------------------------------
# Include custom client-lib
# -----------------------------------------
target_include_directories(${PROJECT_NAME} PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/../client-lib
    ${CMAKE_CURRENT_SOURCE_DIR}/../client-lib/base
)

# Set proper link paths depending on config
if(WIN32)
    if(CMAKE_BUILD_TYPE STREQUAL "Debug")
        target_link_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_BINARY_DIR}/../client-lib/debug)
    else()
        target_link_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_BINARY_DIR}/../client-lib/release)
    endif()
else()
    target_link_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_BINARY_DIR}/../client-lib)
endif()

target_link_libraries(${PROJECT_NAME} PRIVATE client-lib)

# -----------------------------------------
# Install (optional)
# -----------------------------------------
include(GNUInstallDirs)
install(TARGETS ${PROJECT_NAME}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

# -----------------------------------------
# Optional: pass QT_DEPRECATED_WARNINGS
# -----------------------------------------
target_compile_definitions(${PROJECT_NAME} PRIVATE QT_DEPRECATED_WARNINGS)
