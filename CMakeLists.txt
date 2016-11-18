cmake_minimum_required(VERSION 2.8)

project( Real3D )

# Default install directory
set(CMAKE_INSTALL_PREFIX ..)

# Auto-generating functions write their headers into the build directory.
# Hence the build directory should be included.
set( CMAKE_INCLUDE_CURRENT_DIR ON )
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/cmake)

file(GLOB SRC
  "*.cpp"
  "qt/*.cpp"
  "other/*.cpp"
  "maths/*.cpp"
  "opengl/*.cpp"
)

# Configure OpenGL
FIND_PACKAGE(OpenGL REQUIRED)
if(NOT OPENGL_FOUND)
  message(ERROR " OPENGL not found!")
endif(NOT OPENGL_FOUND)
set(LIBRARIES ${LIBRARIES} ${OPENGL_LIBRARIES})  

#Configure Glew
find_package(GLEW REQUIRED)
if (NOT GLEW_FOUND)
  message(FATAL_ERROR "Glew required!")
endif()

include_directories(${GLEW_INCLUDE_DIRS})
SET(LIBRARIES ${LIBRARIES} ${GLEW_LIBRARIES})

# Configure OpenCV
find_package( OpenCV COMPONENTS core imgproc highgui REQUIRED )
if (NOT OPENCV_FOUND)
  message(FATAL_ERROR "OpenCV (minimum v. 2.4.10) required!")
endif()
SET(LIBRARIES ${LIBRARIES} ${OpenCV_LIBS})

# Configure Qt4
FIND_PACKAGE(Qt4 COMPONENTS QtCore QtGui QtOpenGL REQUIRED)
if (NOT QT4_FOUND)
  message(FATAL_ERROR "QT (minimum v. 4.8) required!")
endif()
INCLUDE(${QT_USE_FILE})
SET(LIBRARIES ${LIBRARIES} ${QT_QTCORE_LIBRARY} ${QT_QTGUI_LIBRARY} ${QT_QTOPENGL_LIBRARY})

# Moc and ui generation
set( CMAKE_AUTOMOC ON )
set( CMAKE_AUTOUIC ON )

add_executable( Real3D ${SRC} )
target_link_libraries(Real3D
  ${LIBRARIES}
)
install(TARGETS Real3D DESTINATION ./bin)
add_custom_command(TARGET Real3D PRE_BUILD
                   COMMAND ${CMAKE_COMMAND} -E copy_directory
                   ${CMAKE_SOURCE_DIR}/shaders ${CMAKE_INSTALL_PREFIX}/bin/shaders)