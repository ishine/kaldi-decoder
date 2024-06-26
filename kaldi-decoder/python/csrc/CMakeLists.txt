include_directories(${PROJECT_SOURCE_DIR})

set(srcs
  decodable-ctc.cc
  decodable-itf.cc
  faster-decoder.cc
  kaldi-decoder.cc
  lattice-simple-decoder.cc
  simple-decoder.cc
)

pybind11_add_module(_kaldi_decoder ${srcs})
target_link_libraries(_kaldi_decoder PRIVATE kaldi-decoder-core)

if(APPLE)
  execute_process(
    COMMAND "${PYTHON_EXECUTABLE}" -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())"
    OUTPUT_STRIP_TRAILING_WHITESPACE
    OUTPUT_VARIABLE PYTHON_SITE_PACKAGE_DIR
  )
  message(STATUS "PYTHON_SITE_PACKAGE_DIR: ${PYTHON_SITE_PACKAGE_DIR}")
  if(PYTHON_SITE_PACKAGE_DIR STREQUAL "")
    message(WARNING "PYTHON_SITE_PACKAGE_DIR is empty!")
  else()
    target_link_libraries(_kaldi_decoder PRIVATE "-Wl,-rpath,${PYTHON_SITE_PACKAGE_DIR}")
  endif()
endif()

if(NOT WIN32)
  target_link_libraries(_kaldi_decoder PRIVATE "-Wl,-rpath,${KALDI_DECODER_RPATH_ORIGIN}/kaldi_decoder/lib")
endif()

install(TARGETS _kaldi_decoder
  DESTINATION ../
)
