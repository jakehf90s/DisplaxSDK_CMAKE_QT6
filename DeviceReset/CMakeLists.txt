cmake_minimum_required(VERSION 3.16)

project(DeviceResetConsoleApp LANGUAGES CXX)

# -----------------------------------------------------
# Qt setup
# -----------------------------------------------------
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

# -----------------------------------------------------
# Qt modules (no gui)
# -----------------------------------------------------
find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Core Network Qml)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Core Network Qml)

# -----------------------------------------------------
# Sources
# -----------------------------------------------------
set(SOURCES
    main.cpp
    devicereset.cpp
)

set(HEADERS
    devicereset.h
)

add_executable(${PROJECT_NAME} ${SOURCES} ${HEADERS})

# -----------------------------------------------------
# Link Qt modules
# -----------------------------------------------------
target_link_libraries(${PROJECT_NAME} PRIVATE
    Qt${QT_VERSION_MAJOR}::Core
    Qt${QT_VERSION_MAJOR}::Network
    Qt${QT_VERSION_MAJOR}::Qml
)

# -----------------------------------------------------
# Include client-lib headers
# -----------------------------------------------------
target_include_directories(${PROJECT_NAME} PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/../client-lib
    ${CMAKE_CURRENT_SOURCE_DIR}/../client-lib/base
)

# -----------------------------------------------------
# Link to external client-lib
# -----------------------------------------------------
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

# -----------------------------------------------------
# Compiler Definitions
# -----------------------------------------------------
target_compile_definitions(${PROJECT_NAME} PRIVATE QT_DEPRECATED_WARNINGS)
# Uncomment to block deprecated APIs before Qt 6:
# target_compile_definitions(${PROJECT_NAME} PRIVATE QT_DISABLE_DEPRECATED_BEFORE=0x060000)

# -----------------------------------------------------
# Console-only app (skip GUI / bundle)
# -----------------------------------------------------
set_target_properties(${PROJECT_NAME} PROPERTIES
    WIN32_EXECUTABLE OFF
    MACOSX_BUNDLE OFF
)

# -----------------------------------------------------
# Install (optional)
# -----------------------------------------------------
include(GNUInstallDirs)

if(CMAKE_SYSTEM_NAME STREQUAL "QNX")
    set(INSTALL_BIN_PATH "/tmp/${PROJECT_NAME}/bin")
elseif(UNIX AND NOT ANDROID)
    set(INSTALL_BIN_PATH "/opt/${PROJECT_NAME}/bin")
else()
    set(INSTALL_BIN_PATH ${CMAKE_INSTALL_BINDIR})
endif()

install(TARGETS ${PROJECT_NAME}
    RUNTIME DESTINATION ${INSTALL_BIN_PATH}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

# Install readme
install(FILES readme.md DESTINATION ${CMAKE_INSTALL_DOCDIR})
