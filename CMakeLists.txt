cmake_minimum_required(VERSION 3.14)

project(DisplaxSDKCMake LANGUAGES CXX)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Core)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Core)


add_subdirectory(client-lib)
add_subdirectory(BasicPrintDeviceInfo)
add_subdirectory(DeviceFrameTouch)
add_subdirectory(DeviceLoadSaveSettings)
add_subdirectory(DeviceReset)
add_subdirectory(NetworkDeviceInfo)
add_subdirectory(NetworkDeviceInfoExtended)
add_subdirectory(ActiveTags)
