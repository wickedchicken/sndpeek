# This file is part of the sndpeek application.
# It builds the Marsyas library.
# It has no (non-system) dependencies.

SET(SOURCES_UTIL_SNDFILE
		util_sndfile.c
		)

SET(INCLUDE_DIRS ${CMAKE_CURRENT_SOURCE_DIR})

ADD_LIBRARY(util_sndfile ${SOURCES_UTIL_SNDFILE})
TARGET_INCLUDE_DIRECTORIES(util_sndfile PRIVATE ${INCLUDE_DIRS})
