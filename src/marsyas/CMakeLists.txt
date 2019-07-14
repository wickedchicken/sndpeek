# This file is part of the sndpeek application.
# It builds the Marsyas library.
# It has no (non-system) dependencies.

SET(SOURCES_MARSYAS
		AutoCorrelation.cpp
		Centroid.cpp
		Communicator.cpp
		DownSampler.cpp
		Flux.cpp
		fmatrix.cpp
		fvec.cpp
		Hamming.cpp
		LPC.cpp
		MagFFT.cpp
		MarSignal.cpp
		MFCC.cpp
		NormRMS.cpp
		RMS.cpp
		Rolloff.cpp
		System.cpp
		)

SET(INCLUDE_DIRS ${CMAKE_CURRENT_SOURCE_DIR})

ADD_LIBRARY(marsyas ${SOURCES_MARSYAS})
TARGET_INCLUDE_DIRECTORIES(marsyas PRIVATE ${INCLUDE_DIRS})
