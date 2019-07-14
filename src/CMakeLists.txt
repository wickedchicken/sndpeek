# This file is part of the sndpeek application.
# It builds the sndpeek executable.


SET(SOURCES_SNDPEEK_BASE
		chuck_fft.c
		RtAudio.cpp
		sndpeek.cpp
		Stk.cpp
		Thread.cpp
		WvIn.cpp
		WvOut.cpp
		)
SET(SOURCES_SNDPEEK
		${SOURCES_SNDPEEK_BASE}
		)

SET(UTIL_SNDFILE_SOURCES_DIR util_sndfile)

SET(MARSYAS_SOURCES_DIR marsyas)
SET(MARSYAS_LIBRARY_TARGET marsyas)

FIND_PACKAGE(Threads)

SET(CMAKE_C_FLAGS "-O3 -c")
SET(LIBS ${CMAKE_THREAD_LIBS_INIT} glut GL GLU Xmu X11 Xext Xi m sndfile ${MARSYAS_LIBRARY_TARGET})

SET(INCLUDE_DIRS ${CMAKE_CURRENT_SOURCE_DIR} ${UTIL_SNDFILE_SOURCES_DIR} ${MARSYAS_SOURCES_DIR})

IF(NOT SOUND_SYSTEM)
	IF("${CMAKE_SYSTEM_NAME}" STREQUAL "Windows")
		SET(SOUND_SYSTEM "WIN32")
	ELSEIF("${CMAKE_SYSTEM_NAME}" STREQUAL "Darwin")
		SET(SOUND_SYSTEM "OSX")
	ELSE()
		SET(SOUND_SYSTEM "ALSA")
	ENDIF()
ENDIF()

IF("${SOUND_SYSTEM}" STREQUAL "ALSA")
	ADD_DEFINITIONS(-D__LINUX_ALSA__ -D__LITTLE_ENDIAN__)
	SET(LIBS ${LIBS} -lasound)
ELSEIF("${SOUND_SYSTEM}" STREQUAL "JACK")
	ADD_DEFINITIONS(-D__LINUX_JACK__ -D__LITTLE_ENDIAN__)
	SET(LIBS ${LIBS} -lasound -ljack)
ELSEIF("${SOUND_SYSTEM}" STREQUAL "OSS")
	add_definitions(-D__LINUX_OSS__ -D__LITTLE_ENDIAN__)
	SET(LIBS ${LIBS} -lstdc++)
ELSEIF("${SOUND_SYSTEM}" STREQUAL "OSX")
	ADD_DEFINITIONS(-D__MACOSX_CORE__)
	SET(LIBS -framework CoreAudio -framework CoreMIDI -framework CoreFoundation -framework OpenGL -framework GLUT -framework Foundation -framework AppKit)
	SET(SOURCES_SNDPEEK
			${SOURCES_SNDPEEK}
			${SOURCES_SNDUTIL}
			)
ELSEIF("${SOUND_SYSTEM}" STREQUAL "OSX-UB")
	ADD_DEFINITIONS(-D__MACOSX_CORE__)
	SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -arch ppc -arch i386 -isysroot /Developer/SDKs/MacOSX10.4u.sdk")
	SET(LIBS -framework CoreAudio -framework CoreMIDI -framework CoreFoundation -framework OpenGL -framework GLUT -framework Foundation -framework AppKit -lstdc++ -lm -arch ppc -arch i386 -isysroot /Developer/SDKs/MacOSX10.4u.sdk)
	SET(SOURCES_SNDPEEK
			${SOURCES_SNDPEEK}
			${SOURCES_SNDUTIL}
			)
	#SF_OBJ=util_sndfile.o

	#-----------------------------------------------------------------------------
	# by default, ChucK uses a pre-configured libsndfile...
	# uncomment the next 3 lines to use libsndfile on your system
	#-----------------------------------------------------------------------------
	#FLAGS+= -D__CK_SNDFILE_NATIVE__
	#LIBS+= -lsndfile
	#SF_OBJ=
ELSEIF("${SOUND_SYSTEM}" STREQUAL "WIN32")
	ADD_SUBDIRECTORY(${UTIL_SNDFILE_SOURCES_DIR})
	SET(INCLUDE_DIRS ${INCLUDE_DIRS} /usr/include/directx/)
	ADD_DEFINITIONS(-D__WINDOWS_DS__ -D__WINDOWS_PTHREAD__ -D__LITTLE_ENDIAN__)
	SET(LIBS dsound dxguid winmm pthread opengl32 glu32 glut32)
	SET(SOURCES_SNDPEEK
			${SOURCES_SNDPEEK}
			${SOURCES_SNDUTIL}
			)
ENDIF()

ADD_SUBDIRECTORY(marsyas)

ADD_EXECUTABLE(sndpeek ${SOURCES_SNDPEEK})
TARGET_INCLUDE_DIRECTORIES(sndpeek PRIVATE ${INCLUDE_DIRS})
TARGET_LINK_LIBRARIES(sndpeek ${LIBS})
