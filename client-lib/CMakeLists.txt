cmake_minimum_required(VERSION 3.16)

project(displaxclientlib LANGUAGES CXX)

# -----------------------------------------
# Qt Setup
# -----------------------------------------
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)

# Static library
add_library(${PROJECT_NAME} STATIC)

# -----------------------------------------
# Qt Modules (no GUI)
# -----------------------------------------
find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Core Network Qml Quick)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Core Network Qml Quick)

target_link_libraries(${PROJECT_NAME} PRIVATE
    Qt${QT_VERSION_MAJOR}::Core
    Qt${QT_VERSION_MAJOR}::Network
    Qt${QT_VERSION_MAJOR}::Qml
    Qt${QT_VERSION_MAJOR}::Quick
)

# -----------------------------------------
# Include Paths (from INCLUDEPATH)
# -----------------------------------------
target_include_directories(${PROJECT_NAME}
    PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/
        ${CMAKE_CURRENT_SOURCE_DIR}/communication
        ${CMAKE_CURRENT_SOURCE_DIR}/base
)

# -----------------------------------------
# Compile Definitions (from DEFINES)
# -----------------------------------------
target_compile_definitions(${PROJECT_NAME}
    PUBLIC
        DPX_CLIENT_LIB
        QT_DEPRECATED_WARNINGS
)

# -----------------------------------------
# Sources (from SOURCES)
# -----------------------------------------
target_sources(${PROJECT_NAME}
    PRIVATE
        base/entitybool.cpp
        base/entitycontext.cpp
        base/entityenum.cpp
        base/entityfloat.cpp
        base/entityframe.cpp
        base/entityfunction.cpp
        base/entityint.cpp
        base/entityjsonvalue.cpp
        base/entitysettings.cpp
        base/entitystring.cpp
        base/entitytouchevents.cpp
        dpxclientlib.cpp
        communication/dpxcomconnection.cpp
        communication/dpxcommanager.cpp
        communication/dpxcompeermanager.cpp
        communication/dpxcomurl.cpp
        base/device.cpp
        base/entity.cpp
        base/action.cpp
        base/session.cpp
        base/sessionmanager.cpp
)

# -----------------------------------------
# Headers (for IDEs and for clarity)
# -----------------------------------------
target_sources(${PROJECT_NAME}
    PUBLIC
        base/entitybool.h
        base/entityenum.h
        base/entityfloat.h
        base/entityframe.h
        base/entityfunction.h
        base/entityint.h
        base/entityjsonvalue.h
        base/entitysettings.h
        base/entitystring.h
        base/entitytouchevents.h
        displaxclientlib_global.h
        dpxclientlib.h
        communication/dpxcomconnection.h
        communication/dpxcommanager.h
        communication/dpxcompeermanager.h
        communication/dpxcomurl.h
        base/device.h
        base/entity.h
        base/entitycontext.h
        base/action.h
        base/session.h
        base/sessionmanager.h
)

# -----------------------------------------
# Install Rules
# -----------------------------------------
include(GNUInstallDirs)

# Unix-specific library install path
if(UNIX AND NOT APPLE)
    set(LIB_INSTALL_DIR "/usr/lib")
else()
    set(LIB_INSTALL_DIR ${CMAKE_INSTALL_LIBDIR})
endif()

install(TARGETS ${PROJECT_NAME}
    ARCHIVE DESTINATION ${LIB_INSTALL_DIR}
)

install(FILES readme.md DESTINATION ${CMAKE_INSTALL_DOCDIR})
