FILE(GLOB service_INCLUDES ${CMAKE_CURRENT_SOURCE_DIR}/src/*.h)
FILE(GLOB service_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)
QT5_WRAP_CPP(service_MOC_out ${service_INCLUDES})

IF(VEYON_BUILD_LINUX)

	ADD_SUBDIRECTORY(helpers)

ENDIF(VEYON_BUILD_LINUX)

IF(VEYON_BUILD_WIN32)

	ADD_DEFINITIONS(-DUNICODE -D_UNICODE)
	INCLUDE_DIRECTORIES(${ultravnc_DIR}/winvnc/winvnc)

	SET(WINRC "${CMAKE_CURRENT_BINARY_DIR}/veyon-service-rc.obj")
	ADD_CUSTOM_COMMAND(OUTPUT ${WINRC}
						COMMAND ${WINDRES}
							-I${CMAKE_CURRENT_SOURCE_DIR}
							-o${WINRC}
							-i${CMAKE_CURRENT_BINARY_DIR}/veyon-service.rc
						DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/veyon-service.rc)

ENDIF(VEYON_BUILD_WIN32)

INCLUDE_DIRECTORIES(${CMAKE_CURRENT_BINARY_DIR} ${CMAKE_CURRENT_SOURCE_DIR}/src)

ADD_EXECUTABLE(veyon-service ${service_SOURCES} ${service_INCLUDES} ${service_MOC_out} ${WINRC})
TARGET_LINK_LIBRARIES(veyon-service veyon-core)

INSTALL(TARGETS veyon-service RUNTIME DESTINATION bin)

IF(VEYON_BUILD_WIN32)
	SET_TARGET_PROPERTIES(veyon-service PROPERTIES LINK_FLAGS -mwindows)
ENDIF(VEYON_BUILD_WIN32)

TARGET_LINK_LIBRARIES(veyon-service
        Qt5::Gui
        Qt5::Network
        Qt5::Widgets
	)

COTIRE_VEYON(veyon-service)
