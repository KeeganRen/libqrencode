#if HAVE_SDL
#sdlPROGRAMS = view_qrcode
#endif

#noinst_PROGRAMS = test_qrinput test_bitstream test_estimatebit \
#				  test_qrspec test_rs test_qrencode prof_qrencode \
#				  test_mqrspec test_split test_monkey test_mask test_mmask \
#				  create_frame_pattern create_mqr_frame_pattern \
#				  $(sdlPROGRAMS)
#noinst_HEADERS = common.h
#if HAVE_LIBPTHREAD
#noinst_PROGRAMS += pthread_qrencode
#endif

#EXTRA_DIST = frame

include_directories (${CMAKE_CURRENT_SOURCE_DIR})

##____________________________________________________________________
## Programs to create test patterns

if (PNG_LIBRARIES)

  add_executable (create_frame_pattern create_frame_pattern.c)
  target_link_libraries (create_frame_pattern qrencode ${PNG_LIBRARIES})

  add_executable (create_mqr_frame_pattern create_mqr_frame_pattern.c)
  target_link_libraries (create_mqr_frame_pattern qrencode ${PNG_LIBRARIES})

endif (PNG_LIBRARIES)
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

  ## Add test
  add_test (${test_program} ${test_program})
  
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

#create_frame_pattern_SOURCES = create_frame_pattern.c
#create_frame_pattern_CFLAGS = $(png_CFLAGS)
#create_frame_pattern_LDADD = ../libqrencode.la $(png_LIBS)

#create_mqr_frame_pattern_SOURCES = create_mqr_frame_pattern.c
#create_mqr_frame_pattern_CFLAGS = $(png_CFLAGS)
#create_mqr_frame_pattern_LDADD = ../libqrencode.la $(png_LIBS)

#if HAVE_SDL
#view_qrcode_SOURCES = view_qrcode.c
#view_qrcode_CFLAGS= $(SDL_CFLAGS)
#view_qrcode_LDADD = ../libqrencode.la $(SDL_LIBS)
#endif
