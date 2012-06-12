
include_directories (${CMAKE_CURRENT_SOURCE_DIR})

## ==============================================================================
##
##  Instructions to build and run the test programs
##
##  1. libdecoder library for common functionality.
##  2. Support programs to create test patterns.
##  3. Programs which are part of the test suite.
##
## ==============================================================================

##____________________________________________________________________
## Library: libdecoder

add_library (decoder
  decoder.c
  decoder.h
  )

if (ICONV_LIBRARIES)
  set (decoder_link_libraries qrencode ${ICONV_LIBRARIES})
else (ICONV_LIBRARIES)
  set (decoder_link_libraries qrencode)
endif (ICONV_LIBRARIES)

target_link_libraries (decoder ${decoder_link_libraries})

##____________________________________________________________________
## Programs to create test patterns

if (PNG_LIBRARIES)

    ## create_frame_pattern
    add_executable (create_frame_pattern create_frame_pattern.c)
    target_link_libraries (create_frame_pattern qrencode ${PNG_LIBRARIES})
    add_test (create_frame_pattern create_frame_pattern frame)
    
    ## create_mqr_frame_pattern
    add_executable (create_mqr_frame_pattern create_mqr_frame_pattern.c)
    target_link_libraries (create_mqr_frame_pattern qrencode ${PNG_LIBRARIES})
    add_test (create_mqr_frame_pattern create_mqr_frame_pattern)
    
endif (PNG_LIBRARIES)

##____________________________________________________________________
## Test programs

foreach (test_program
    test_qrinput
    test_bitstream
    test_estimatebit
    test_qrspec
    test_mqrspec
    test_rs
    test_qrencode
    test_split
    test_monkey
    test_mask
    prof_qrencode
    )
  
  ## Compiler instructions
  add_executable(${test_program} ${test_program}.c)
  
  ## Linker instructions
  target_link_libraries (${test_program} decoder ${decoder_link_libraries})

  ## Add test ...
  add_test (${test_program} ${test_program})

  ## ... and its dependencies
  set_tests_properties(${test_program}
    PROPERTIES
    DEPENDS create_frame_pattern
    DEPENDS create_mqr_frame_pattern
    )
  
endforeach (test_program)

##____________________________________________________________________
## Programs that require pthread

if (CMAKE_THREAD_LIBS_INIT)

  add_executable (pthread_qrencode pthread_qrencode.c)

  target_link_libraries (pthread_qrencode decoder ${decoder_link_libraries})

  set_target_properties (pthread_qrencode
    PROPERTIES
    LINK_FLAGS "${CMAKE_THREAD_LIBS_INIT}"
    )

endif (CMAKE_THREAD_LIBS_INIT)

#if HAVE_SDL
#view_qrcode_SOURCES = view_qrcode.c
#view_qrcode_CFLAGS= $(SDL_CFLAGS)
#view_qrcode_LDADD = ../libqrencode.la $(SDL_LIBS)
#endif

