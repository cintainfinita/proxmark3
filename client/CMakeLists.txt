cmake_minimum_required(VERSION 3.10)
project(proxmark3)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

if(CMAKE_VERSION VERSION_LESS "3.7.0")
    set(CMAKE_INCLUDE_CURRENT_DIR ON)
endif()

set(CMAKE_CXX_STANDARD 14)

if(APPLE AND EXISTS /usr/local/opt/qt5)
    # Homebrew installs Qt5 (up to at least 5.11.0) in
    # /usr/local/qt5. Ensure that it can be found by CMake
    # since it is not in the default /usr/local prefix.
    # Add it to PATHS so that it doesn't override the
    # CMAKE_PREFIX_PATH environment variable.
    # QT_FIND_PACKAGE_OPTIONS should be passed to find_package,
    # e.g. find_package(Qt5Core ${QT_FIND_PACKAGE_OPTIONS})
    list(APPEND QT_FIND_PACKAGE_OPTIONS PATHS /usr/local/opt/qt5)
endif()
set(QT_PACKAGELIST
    Qt5Core
    Qt5Widgets
    Qt5Gui
)
foreach(_qt_package IN LISTS QT_PACKAGELIST)
    find_package(${_qt_package} ${QT_FIND_PACKAGE_OPTIONS})
endforeach()

set(Qt5_FOUND ON)
foreach(_qt_package IN LISTS QT_PACKAGELIST)
    if(NOT ${_qt_package}_FOUND)
        set(Qt5_FOUND OFF)
    endif(NOT ${_qt_package}_FOUND)
endforeach()

SET (CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

INCLUDE(FindSSE)
if(NOT SSE2_FOUND)
    MESSAGE(STATUS "Could not find hardware support for SSE2 on this machine.")
endif(NOT SSE2_FOUND)
if(NOT SSE3_FOUND)
    MESSAGE(STATUS "Could not find hardware support for SSE3 on this machine.")
endif(NOT SSE3_FOUND)
if(NOT SSSE3_FOUND)
    MESSAGE(STATUS "Could not find hardware support for SSSE3 on this machine.")
endif(NOT SSSE3_FOUND)
if(NOT SSE4_1_FOUND)
    MESSAGE(STATUS "Could not find hardware support for SSE4.1 on this machine.")
endif(NOT SSE4_1_FOUND)
if(NOT AVX_FOUND)
    MESSAGE(STATUS "Could not find hardware support for AVX on this machine.")
endif(NOT AVX_FOUND)
if(NOT AVX2_FOUND)
    MESSAGE(STATUS "Could not find hardware support for AVX2 on this machine.")
endif(NOT AVX2_FOUND)
if(NOT AVX512_FOUND)
    MESSAGE(STATUS "Could not find hardware support for AVX512 on this machine.")
endif(NOT AVX512_FOUND)

add_subdirectory(deps)

#file(GLOB_RECURSE TARGET_HEADERS ${CMAKE_CURRENT_LIST_DIR}/src/*.h)
#file(GLOB_RECURSE TARGET_SOURCES ${CMAKE_CURRENT_LIST_DIR}/src/*.cpp)

set (TARGET_SOURCES
        src/proxmark3.c
        ../common/commonutil.c
        ../common/util_posix.c
        ../common/parity.c
        ../common/bucketsort.c
        ../common/crapto1/crapto1.c
        ../common/crapto1/crypto1.c
        ../common/crc.c
        ../common/crc16.c
        ../common/crc32.c
        ../common/crc64.c
        ../common/lfdemod.c
        ../common/legic_prng.c
        ../common/iso15693tools.c
        ../common/cardhelper.c
        ../common/generator.c
        deps/cliparser/argtable3.c
        deps/cliparser/cliparser.c
        deps/hardnested/hardnested_bruteforce.c
        src/crypto/asn1dump.c
        src/crypto/asn1utils.c
        src/crypto/libpcrypto.c
        src/emv/test/cda_test.c
        src/emv/test/crypto_test.c
        src/emv/test/cryptotest.c
        src/emv/test/dda_test.c
        src/emv/test/sda_test.c
        src/emv/apduinfo.c
        src/emv/cmdemv.c
        src/emv/crypto.c
        src/emv/crypto_polarssl.c
        src/emv/dol.c
        src/emv/dump.c
        src/emv/emv_pk.c
        src/emv/emv_pki.c
        src/emv/emv_pki_priv.c
        src/emv/emv_roca.c
        src/emv/emv_tags.c
        src/emv/emvcore.c
        src/emv/emvjson.c
        src/emv/tlv.c
        src/fido/additional_ca.c
        src/fido/cbortools.c
        src/fido/cose.c
        src/fido/fidocore.c
        src/loclass/cipher.c
        src/loclass/cipherutils.c
        src/loclass/elite_crack.c
        src/loclass/hash1_brute.c
        src/loclass/ikeys.c
        src/mifare/mad.c
        src/mifare/mfkey.c
        src/mifare/mifare4.c
        src/mifare/mifaredefault.c
        src/mifare/mifarehost.c
        src/mifare/ndef.c
        src/mifare/desfire_crypto.c
        src/uart/uart_posix.c
        src/uart/uart_win32.c
        src/ui/overlays.ui
        src/aidsearch.c
        src/cmdanalyse.c
        src/cmdcrc.c
        src/cmddata.c
        src/cmdflashmem.c
        src/cmdflashmemspiffs.c
        src/cmdhf.c
        src/cmdhf14a.c
        src/cmdhf14b.c
        src/cmdhf15.c
        src/cmdhfcryptorf.c
        src/cmdhfepa.c
        src/cmdhffelica.c
        src/cmdhffido.c
        src/cmdhficlass.c
        src/cmdhflegic.c
        src/cmdhflist.c
        src/cmdhflto.c
        src/cmdhfmf.c
        src/cmdhfmfdes.c
        src/cmdhfmfhard.c
        src/cmdhfmfp.c
        src/cmdhfmfu.c
        src/cmdhfthinfilm.c
        src/cmdhftopaz.c
        src/cmdhw.c
        src/cmdlf.c
        src/cmdlfawid.c
        src/cmdlfcotag.c
        src/cmdlfem4x.c
        src/cmdlffdx.c
        src/cmdlfgallagher.c
        src/cmdlfguard.c
        src/cmdlfhid.c
        src/cmdlfhitag.c
        src/cmdlfindala.c
        src/cmdlfio.c
        src/cmdlfjablotron.c
        src/cmdlfkeri.c
        src/cmdlfmotorola.c
        src/cmdlfnedap.c
        src/cmdlfnexwatch.c
        src/cmdlfnoralsy.c
        src/cmdlfpac.c
        src/cmdlfparadox.c
        src/cmdlfpcf7931.c
        src/cmdlfpresco.c
        src/cmdlfpyramid.c
        src/cmdlfsecurakey.c
        src/cmdlft55xx.c
        src/cmdlfti.c
        src/cmdlfverichip.c
        src/cmdlfviking.c
        src/cmdlfvisa2000.c
        src/cmdmain.c
        src/cmdparser.c
        src/cmdscript.c
        src/cmdsmartcard.c
        src/cmdtrace.c
        src/cmdusart.c
        src/cmdwiegand.c
        src/comms.c
        src/fileutils.c
        src/flash.c
        src/graph.c
        src/preferences.c
        src/pm3_binlib.c
        src/pm3_bitlib.c
        src/prng.c
        src/scandir.c
        src/scripting.c
        src/tea.c
        src/ui.c
        src/util.c
        src/whereami.c
        src/wiegand_formats.c
        src/wiegand_formatutils.c
        )

set(ADDITIONAL_SRC "")
set(ADDITIONAL_LNK "")

set(X86_CPUS x86 x86_64 i686)

message(STATUS "CMAKE_SYSTEM_PROCESSOR := ${CMAKE_SYSTEM_PROCESSOR}")

if (APPLE)
    message("Apple device detected.")
    set(ADDITIONAL_SRC src/util_darwin.h  src/util_darwin.m ${ADDITIONAL_SRC})
    set(ADDITIONAL_LNK "-framework Foundation" "-framework AppKit")
endif (APPLE)

if (MINGW)
    set(CMAKE_CXX_FLAGS "-mno-ms-bitfields -fexec-charset=cp850 ${CMAKE_CXX_FLAGS}")
endif (MINGW)

if (Qt5_FOUND)
    message("Qt5 library found, building gui :)")
    set (TARGET_SOURCES
            src/proxgui.cpp
            src/proxguiqt.cpp
            ${TARGET_SOURCES})

    add_definitions("-DHAVE_GUI")
    set(ADDITIONAL_LNK Qt5::Core Qt5::Widgets Qt5::Gui ${ADDITIONAL_LNK})
else (Qt5_FOUND)
    message("Qt5 library not found, not building gui")
    set(TARGET_SOURCES
            src/guidummy.cpp
            ${TARGET_SOURCES})
endif (Qt5_FOUND)

add_executable(
        proxmark3
        ${TARGET_SOURCES}
        ${ADDITIONAL_SRC}
)

target_compile_options(proxmark3 PUBLIC -Wall -Werror -O3)

target_include_directories(proxmark3 PRIVATE
        ..
        ../common
        ../include
        deps
        src
        src/uart
)

if (APPLE)
    set_target_properties(proxmark3 PROPERTIES LINK_FLAGS "-Wl,-F/Library/Frameworks, -L/usr/local/opt/readline/lib")
    set_target_properties(proxmark3 PROPERTIES COMPILE_FLAGS "-I/usr/local/opt/readline/include")
endif (APPLE)


find_library(jansson REQUIRED)
find_library(tinycbor REQUIRED)
find_library(lua REQUIRED)
find_library(mbedtls REQUIRED)
find_library(reveng REQUIRED)
find_library(z REQUIRED)
find_library(hardnested REQUIRED)

target_include_directories(proxmark3 PUBLIC mbedtls jansson lua tinycbor amiibo reveng z hardnested)
target_link_libraries(proxmark3 readline pthread m mbedtls jansson lua tinycbor amiibo reveng z hardnested ${ADDITIONAL_LNK})

install(TARGETS proxmark3 DESTINATION "bin")
install(DIRECTORY cmdscripts lualibs luascripts resources dictionaries DESTINATION "share/proxmark3")

add_custom_command(OUTPUT lualibs/pm3_cmd.lua
        COMMAND "awk -f pm3_cmd_h2lua.awk ../include/pm3_cmd.h >  lualibs/pm3_cmd.lua"
        COMMENT "Creating lualibs/pm3_cmd.lua"
        )

add_custom_command(OUTPUT lualibs/mfc_default_keys.lua
        COMMAND "awk -f default_keys_dic2lua.awk mfc_default_keys.dic >  lualibs/mfc_default_keys.lua"
        COMMENT "Creating lualibs/mfc_default_keys.lua"
        )

#"make package" will trigger this
SET(CPACK_GENERATOR "DEB")
SET(CPACK_DEBIAN_PACKAGE_MAINTAINER "Iceman")
INCLUDE(CPack)
