cmake_minimum_required(VERSION 3.22)
project(avro)

list(APPEND CMAKE_MODULE_PATH ../lang/c++/cmake)

find_package(Snappy)

find_package (Boost 1.74 REQUIRED
  COMPONENTS iostreams system)

add_library(avro
  ../lang/c++/impl/Compiler.cc
  ../lang/c++/impl/Node.cc
  ../lang/c++/impl/LogicalType.cc
  ../lang/c++/impl/NodeImpl.cc
  ../lang/c++/impl/ResolverSchema.cc
  ../lang/c++/impl/Schema.cc
  ../lang/c++/impl/Types.cc
  ../lang/c++/impl/ValidSchema.cc
  ../lang/c++/impl/Zigzag.cc
  ../lang/c++/impl/BinaryEncoder.cc
  ../lang/c++/impl/BinaryDecoder.cc
  ../lang/c++/impl/Stream.cc
  ../lang/c++/impl/FileStream.cc
  ../lang/c++/impl/Generic.cc
  ../lang/c++/impl/GenericDatum.cc
  ../lang/c++/impl/DataFile.cc
  ../lang/c++/impl/parsing/Symbol.cc
  ../lang/c++/impl/parsing/ValidatingCodec.cc
  ../lang/c++/impl/parsing/JsonCodec.cc
  ../lang/c++/impl/parsing/ResolvingDecoder.cc
  ../lang/c++/impl/json/JsonIO.cc
  ../lang/c++/impl/json/JsonDom.cc
  ../lang/c++/impl/Resolver.cc
  ../lang/c++/impl/Validator.cc
  ../lang/c++/impl/CustomAttributes.cc)

if(SNAPPY_FOUND)
  target_compile_definitions(avro PUBLIC SNAPPY_CODEC_AVAILABLE)
endif()

target_link_libraries(avro
    Boost::iostreams
    Boost::system)

target_include_directories(avro
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../lang/c++/include/avro>
    $<INSTALL_INTERFACE:include/avro>)

add_library(Avro::avro ALIAS avro)

install(
  TARGETS avro
  EXPORT AvroTargets
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION lib)

install(
  DIRECTORY ../lang/c++/include/avro/
  DESTINATION include/avro
  FILES_MATCHING PATTERN *.hh)

install(EXPORT AvroTargets NAMESPACE Avro:: DESTINATION share/avro)

include(CMakePackageConfigHelpers)

configure_package_config_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/AvroConfig.cmake.in
  ${CMAKE_CURRENT_BINARY_DIR}/AvroConfig.cmake
  INSTALL_DESTINATION share/avro)

write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/AvroConfigVersion.cmake
  VERSION 1.0.0
  COMPATIBILITY SameMajorVersion)

install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/AvroConfig.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/AvroConfigVersion.cmake
  DESTINATION share/avro)
