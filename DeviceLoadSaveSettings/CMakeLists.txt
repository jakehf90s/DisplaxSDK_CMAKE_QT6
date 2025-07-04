cmake_minimum_required(VERSION 3.16)

project(DeviceSettingsConsoleApp LANGUAGES CXX)

# C++11 + Qt convenience settings
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

# ---------------------------------------------
# Qt modules (no GUI)
# ---------------------------------------------
find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Core Network Qml Quick)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Core Network Qml Quick)

# ---------------------------------------------
# Source and header files
# ---------------------------------------------
set(SOURCES
    main.cpp
    devicesettings.cpp
)

set(HEADERS
    devicesettings.h
)

add_executable(${PROJECT_NAME} ${SOURCES} ${HEADERS})

# ---------------------------------------------
# Link Qt libraries
# ---------------------------------------------
target_link_libraries(${PROJECT_NAME} PRIVATE
    Qt${QT_VERSION_MAJOR}::Core
    Qt${QT_VERSION_MAJOR}::Network
    Qt${QT_VERSION_MAJOR}::Qml
    Qt${QT_VERSION_MAJOR}::Quick
)

# ---------------------------------------------
# Optional deprecated API warnings
# ---------------------------------------------
target_compile_definitions(${PROJECT_NAME} PRIVATE QT_DEPRECATED_WARNINGS)
# Uncomment to block APIs deprecated before Qt 6
# target_compile_definitions(${PROJECT_NAME} PRIVATE QT_DISABLE_DEPRECATED_BEFORE=0x060000)

# ---------------------------------------------
# Include and link external client-lib
# ---------------------------------------------
target_include_directories(${PROJECT_NAME} PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/../client-lib
    ${CMAKE_CURRENT_SOURCE_DIR}/../client-lib/base
)

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

# ---------------------------------------------
# Console mode, no GUI or bundle
# ---------------------------------------------
set_target_properties(${PROJECT_NAME} PROPERTIES
    WIN32_EXECUTABLE OFF
    MACOSX_BUNDLE OFF
)

# ---------------------------------------------
# Install paths (same as .pro)
# ---------------------------------------------
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

# ---------------------------------------------
# Extra files
# ---------------------------------------------
install(FILES readme.md DESTINATION ${CMAKE_INSTALL_DOCDIR})
