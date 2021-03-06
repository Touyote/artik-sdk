CMAKE_MINIMUM_REQUIRED	( VERSION 2.8 )
PROJECT			( artik-sdk-base C CXX )

PKG_CHECK_MODULES ( GLIB REQUIRED glib-2.0 )
FIND_PACKAGE ( Dl )

SET ( LIB_BASE artik-sdk-base CACHE INTERNAL "" FORCE )
SET ( ARTIK_BASE_INCLUDE_DIR ${LIB_INC}/base CACHE INTERNAL "" FORCE )
SET ( ARTIK_BASE_LIBRARIES ${LIB_BASE} CACHE INTERNAL "" FORCE )

SET ( SRC_BASE
					module/artik_module.c
					module/linux_module.c
					log/artik_log.c
					log/linux_log.c
					loop/artik_loop.c
					loop/linux_loop.c
					time/linux_time.c
					time/artik_time.c
)

SET ( SRC_BASE_CPP
					time/cpp/artik_time.cpp
					loop/cpp/artik_loop.cpp
)

ADD_LIBRARY ( ${LIB_BASE}_c OBJECT ${SRC_BASE} )
TARGET_COMPILE_DEFINITIONS ( ${LIB_BASE}_c PRIVATE "-DEXPORT_API=__attribute__((visibility(\"default\")))")
TARGET_COMPILE_OPTIONS ( ${LIB_BASE}_c PRIVATE "-fvisibility=hidden" "-fPIC" )
TARGET_INCLUDE_DIRECTORIES ( ${LIB_BASE}_c PUBLIC
							 ${LIB_INC}
							 ${ARTIK_BASE_INCLUDE_DIR}
							 ${GLIB_INCLUDE_DIRS}
)


ADD_LIBRARY ( ${LIB_BASE} SHARED $<TARGET_OBJECTS:${LIB_BASE}_c> ${SRC_BASE_CPP} )
TARGET_INCLUDE_DIRECTORIES ( ${LIB_BASE} PUBLIC
							 ${LIB_INC}
							 ${ARTIK_BASE_INCLUDE_DIR}
							 ${GLIB_INCLUDE_DIRS}
							 ${ARTIK_BASE_INCLUDE_DIR}/cpp
)
TARGET_LINK_LIBRARIES ( ${LIB_BASE}
						${GLIB_LIBRARIES}
						${DL_LIBRARIES}
)

SET_TARGET_PROPERTIES ( ${LIB_BASE} PROPERTIES VERSION ${LIB_VERSION_MAJOR}.${LIB_VERSION_MINOR}.${LIB_VERSION_PATCH} SOVERSION ${LIB_VERSION_MAJOR} OUTPUT_NAME ${LIB_BASE})

INSTALL ( TARGETS ${LIB_BASE} LIBRARY DESTINATION lib )
FILE ( GLOB BASE_HEADERS "${LIB_INC}/base/*.h" )
FILE ( GLOB BASE_HEADERS_CPP "${LIB_INC}/base/cpp/*.hh" )
FILE ( GLOB BASE_HEADERS_PLATFORM "${LIB_INC}/base/platform/*.h" )
INSTALL ( FILES ${BASE_HEADERS} DESTINATION include/artik/base )
INSTALL ( FILES ${BASE_HEADERS_CPP} DESTINATION include/artik/base/cpp )
INSTALL ( FILES ${BASE_HEADERS_PLATFORM} DESTINATION include/artik/base/platform )
