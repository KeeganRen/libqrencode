#if HAVE_SDL
#sdlPROGRAMS = view_qrcode
#endif

#noinst_PROGRAMS = test_qrinput test_bitstream test_estimatebit \
#				  test_qrspec test_rs test_qrencode prof_qrencode \
#				  test_mqrspec test_split test_monkey test_mask test_mmask \
#				  create_frame_pattern create_mqr_frame_pattern \
#				  $(sdlPROGRAMS)
#noinst_LIBRARIES = libdecoder.a
#DECODER_LIBS = libdecoder.a $(LIBICONV)
#noinst_HEADERS = common.h
#if HAVE_LIBPTHREAD
#noinst_PROGRAMS += pthread_qrencode
#endif

#EXTRA_DIST = frame

include_directories (${CMAKE_CURRENT_SOURCE_DIR})

## Library: libdecoder
add_library (decoder
  decoder.c
  decoder.h
  )

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
    pthread_qrencode
    )

  add_executable(${test_program} ${test_program}.c)

  target_link_libraries (${test_program} decoder qrencode)

  add_test (${test_program} ${test_program})

endforeach (test_program)

#pthread_qrencode_SOURCES = .c
#pthread_qrencode_LDADD = ../libqrencode.la $(LIBPTHREAD)

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
