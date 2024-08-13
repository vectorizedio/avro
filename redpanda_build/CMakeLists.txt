cmake_minimum_required(VERSION 3.22)
project(avro)

if (EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/../VERSION.txt)
    file(READ "${CMAKE_CURRENT_SOURCE_DIR}/../VERSION.txt" AVRO_VERSION)
else (EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/../VERSION.txt)
    file(READ "${CMAKE_CURRENT_SOURCE_DIR}/../share/VERSION.txt"
        AVRO_VERSION)
endif (EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/../VERSION.txt)

string(REPLACE "\n" "" AVRO_VERSION  ${AVRO_VERSION})
string(REPLACE "." ";" AVRO_VERSION  ${AVRO_VERSION})
list(GET AVRO_VERSION 0 AVRO_VERSION_MAJOR)
list(GET AVRO_VERSION 1 AVRO_VERSION_MINOR)
list(GET AVRO_VERSION 2 AVRO_VERSION_PATCH)
add_definitions (-DAVRO_VERSION="${AVRO_VERSION_MAJOR}.${AVRO_VERSION_MINOR}.${AVRO_VERSION_PATCH}")

list(APPEND CMAKE_MODULE_PATH ../lang/c++/cmake)

find_package(Snappy)

find_package (Boost 1.74 REQUIRED
  COMPONENTS iostreams system program_options regex)

find_package(fmt 8.1.1 REQUIRED)

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
    Boost::system
    fmt::fmt)

target_include_directories(avro
  PUBLIC ../lang/c++/include Boost::boost
  PRIVATE ../lang/c++/include/avro)

add_library(Avro::avro ALIAS avro)

add_executable (avrogencpp ../lang/c++/impl/avrogencpp.cc)
target_include_directories(avrogencpp PRIVATE ../lang/c++/include/avro)
target_link_libraries (avrogencpp avro ${Boost_LIBRARIES} ${SNAPPY_LIBRARIES})
