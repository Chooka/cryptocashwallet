cmake_minimum_required(VERSION 2.8)

include(CryptoCashWallet.cmake)

project(${CN_PROJECT_NAME})

execute_process(COMMAND git log -1 --pretty=format:%h
WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
OUTPUT_VARIABLE GIT_REVISION)

set(CRYPTONOTE_LIB cryptocash)

include_directories(${CMAKE_BINARY_DIR}
    src
    cryptocash/external
    cryptocash/include
    cryptocash/src)

find_package(Qt5Gui REQUIRED)
find_package(Qt5Widgets REQUIRED)

set(Boost_USE_STATIC_LIBS ON)
if(WIN32)
  set(Boost_USE_STATIC_RUNTIME OFF)
else(WIN32)
  set(Boost_USE_STATIC_RUNTIME ON)
endif(WIN32)

find_package(Boost 1.68 REQUIRED COMPONENTS date_time filesystem program_options regex serialization system thread chrono)
if ((${Boost_MAJOR_VERSION} EQUAL 1) AND (${Boost_MINOR_VERSION} EQUAL 54))
	message(SEND_ERROR "Boost version 1.54 is unsupported, more details are available here http://goo.gl/RrCFmA")
endif ()

include_directories(${Boost_INCLUDE_DIRS})
link_directories(${Boost_LIBRARY_DIRS})

set(VERSION "")
configure_file("cryptocash/src/version.h.in" "version.h")
configure_file("src/CryptoNoteWalletConfig.h.in" "CryptoNoteWalletConfig.h")

add_definitions(-DGIT_REVISION=\"${GIT_REVISION}\")

set(CMAKE_AUTOMOC ON)

set(CRYPTONOTE_SOURCES
  cryptocash/external/miniupnpc/connecthostport.c
  cryptocash/external/miniupnpc/igd_desc_parse.c
  cryptocash/external/miniupnpc/minisoap.c
  cryptocash/external/miniupnpc/miniupnpc.c
  cryptocash/external/miniupnpc/miniwget.c
  cryptocash/external/miniupnpc/minixml.c
  cryptocash/external/miniupnpc/portlistingparse.c
  cryptocash/external/miniupnpc/receivedata.c
  cryptocash/external/miniupnpc/upnpcommands.c
  cryptocash/external/miniupnpc/upnpreplyparse.c
  cryptocash/src/Common/Base58.cpp
  cryptocash/src/Common/CommandLine.cpp
  cryptocash/src/Common/Util.cpp
  cryptocash/src/Common/StringTools.cpp
  cryptocash/src/Common/JsonValue.cpp
  cryptocash/src/Common/ConsoleTools.cpp
  cryptocash/src/Common/MemoryInputStream.cpp
  cryptocash/src/Common/PathTools.cpp
  cryptocash/src/Common/StdInputStream.cpp
  cryptocash/src/Common/StdOutputStream.cpp
  cryptocash/src/Common/StreamTools.cpp
  cryptocash/src/Common/StringOutputStream.cpp
  cryptocash/src/Common/StringView.cpp
  cryptocash/src/Common/VectorOutputStream.cpp
  cryptocash/src/crypto/blake256.c
  cryptocash/src/crypto/chacha8.c
  cryptocash/src/crypto/crypto-ops-data.c
  cryptocash/src/crypto/crypto-ops.c
  cryptocash/src/crypto/crypto.cpp
  cryptocash/src/crypto/groestl.c
  cryptocash/src/crypto/hash-extra-blake.c
  cryptocash/src/crypto/hash-extra-groestl.c
  cryptocash/src/crypto/hash-extra-jh.c
  cryptocash/src/crypto/hash-extra-skein.c
  cryptocash/src/crypto/hash.c
  cryptocash/src/crypto/jh.c
  cryptocash/src/crypto/keccak.c
  cryptocash/src/crypto/oaes_lib.c
  cryptocash/src/crypto/random.c
  cryptocash/src/crypto/skein.c
  cryptocash/src/crypto/slow-hash.c
  cryptocash/src/crypto/slow-hash.cpp
  cryptocash/src/crypto/tree-hash.c
  cryptocash/src/CryptoNoteCore/BlockchainIndices.cpp
  cryptocash/src/CryptoNoteCore/BlockchainMessages.cpp
  cryptocash/src/CryptoNoteCore/BlockIndex.cpp
  cryptocash/src/CryptoNoteCore/CoreConfig.cpp
  cryptocash/src/CryptoNoteCore/CryptoNoteBasic.cpp
  cryptocash/src/CryptoNoteCore/CryptoNoteTools.cpp
  cryptocash/src/CryptoNoteCore/Currency.cpp
  cryptocash/src/CryptoNoteCore/MinerConfig.cpp
  cryptocash/src/CryptoNoteCore/Transaction.cpp
  cryptocash/src/CryptoNoteCore/Account.cpp
  cryptocash/src/CryptoNoteCore/Blockchain.cpp
  cryptocash/src/CryptoNoteCore/Checkpoints.cpp
  cryptocash/src/CryptoNoteCore/CryptoNoteBasicImpl.cpp
  cryptocash/src/CryptoNoteCore/Core.cpp
  cryptocash/src/CryptoNoteCore/CryptoNoteFormatUtils.cpp
  cryptocash/src/CryptoNoteCore/CryptoNoteSerialization.cpp
  cryptocash/src/CryptoNoteCore/Difficulty.cpp
  cryptocash/src/CryptoNoteCore/IBlock.cpp
  cryptocash/src/CryptoNoteCore/Miner.cpp
  cryptocash/src/CryptoNoteCore/TransactionExtra.cpp
  cryptocash/src/CryptoNoteCore/TransactionPool.cpp
  cryptocash/src/CryptoNoteCore/TransactionPrefixImpl.cpp
  cryptocash/src/CryptoNoteCore/TransactionUtils.cpp
  cryptocash/src/InProcessNode/InProcessNode.cpp
  cryptocash/src/InProcessNode/InProcessNodeErrors.cpp
  cryptocash/src/NodeRpcProxy/NodeErrors.cpp
  cryptocash/src/NodeRpcProxy/NodeRpcProxy.cpp
  cryptocash/src/P2p/NetNodeConfig.cpp
  cryptocash/src/Serialization/BinaryInputStreamSerializer.cpp
  cryptocash/src/Serialization/BinaryOutputStreamSerializer.cpp
  cryptocash/src/Serialization/JsonInputValueSerializer.cpp
  cryptocash/src/Serialization/JsonOutputStreamSerializer.cpp
  cryptocash/src/Serialization/KVBinaryInputStreamSerializer.cpp
  cryptocash/src/Serialization/KVBinaryOutputStreamSerializer.cpp
  cryptocash/src/Serialization/SerializationOverloads.cpp
  cryptocash/src/Transfers/BlockchainSynchronizer.cpp
  cryptocash/src/Transfers/SynchronizationState.cpp
  cryptocash/src/Transfers/TransfersConsumer.cpp
  cryptocash/src/Transfers/TransfersContainer.cpp
  cryptocash/src/Transfers/TransfersSubscription.cpp
  cryptocash/src/Transfers/TransfersSynchronizer.cpp
  cryptocash/src/Wallet/LegacyKeysImporter.cpp
  cryptocash/src/Wallet/WalletAsyncContextCounter.cpp
  cryptocash/src/Wallet/WalletErrors.cpp
  cryptocash/src/WalletLegacy/KeysStorage.cpp
  cryptocash/src/WalletLegacy/WalletLegacy.cpp
  cryptocash/src/WalletLegacy/WalletHelper.cpp
  cryptocash/src/WalletLegacy/WalletLegacySerializer.cpp
  cryptocash/src/WalletLegacy/WalletLegacySerialization.cpp
  cryptocash/src/WalletLegacy/WalletTransactionSender.cpp
  cryptocash/src/WalletLegacy/WalletUnconfirmedTransactions.cpp
  cryptocash/src/WalletLegacy/WalletUserTransactionsCache.cpp
  cryptocash/src/System/ContextGroup.cpp
  cryptocash/src/System/Event.cpp
  cryptocash/src/System/EventLock.cpp
  cryptocash/src/System/InterruptedException.cpp
  cryptocash/src/System/Ipv4Address.cpp
  cryptocash/src/System/TcpStream.cpp
  cryptocash/src/HTTP/HttpRequest.cpp
  cryptocash/src/HTTP/HttpParser.cpp
  cryptocash/src/HTTP/HttpParserErrorCodes.cpp
  cryptocash/src/HTTP/HttpResponse.cpp
  cryptocash/src/Rpc/HttpClient.cpp
  cryptocash/src/Rpc/JsonRpc.cpp
  cryptocash/src/P2p/NetNode.cpp
  cryptocash/src/P2p/LevinProtocol.cpp
  cryptocash/src/P2p/NetNodeConfig.cpp
  cryptocash/src/P2p/PeerListManager.cpp
  cryptocash/src/CryptoNoteProtocol/CryptoNoteProtocolHandler.cpp
  cryptocash/src/Logging/ILogger.cpp
  cryptocash/src/Logging/LoggerGroup.cpp
  cryptocash/src/Logging/CommonLogger.cpp
  cryptocash/src/Logging/LoggerManager.cpp
  cryptocash/src/Logging/FileLogger.cpp
  cryptocash/src/Logging/StreamLogger.cpp
  cryptocash/src/Logging/ConsoleLogger.cpp
  cryptocash/src/Logging/LoggerMessage.cpp
  cryptocash/src/Logging/LoggerRef.cpp
  cryptocash/src/BlockchainExplorer/BlockchainExplorer.cpp
  cryptocash/src/BlockchainExplorer/BlockchainExplorerDataBuilder.cpp
  cryptocash/src/BlockchainExplorer/BlockchainExplorerErrors.cpp
)

file(GLOB_RECURSE SOURCES src/*.cpp)
file(GLOB_RECURSE HEADERS src/*.h)
file(GLOB_RECURSE FORMS src/gui/ui/*.ui)

set(QRC src/resources.qrc)

qt5_wrap_ui(UIS ${FORMS})
qt5_add_resources(RCC ${QRC})


if (WIN32)
  if (NOT MSVC)
	message(FATAL_ERROR "Only MSVC is supported on this platform")
  endif ()
  add_definitions(/D_CRT_SECURE_NO_WARNINGS /D_WIN32_WINNT=0x0600 /DSTATICLIB)
  include_directories(cryptocash/src/platform/msc)

  set(PLATFORM_DIR Windows)
  set(BUILD_PLATFORM WIN32)
  set(BUILD_RESOURCES src/cryptonotewallet.rc)
  set(QTMAIN Qt5::WinMain)

elseif (UNIX)
  set(CRYPTONOTE_SOURCES ${CRYPTONOTE_SOURCES} cryptonote/external/miniupnpc/minissdpc.c)
  if (APPLE)
    enable_language(ASM)
    file(GLOB_RECURSE OBJC_SOURCES src/*.mm)
    set(SOURCES ${SOURCES} ${OBJC_SOURCES})
    set(PLATFORM_DIR OSX)
    set(MACOSX_BUNDLE_INFO_STRING "Cryptonote GUI wallet")
    set(MACOSX_BUNDLE_LONG_VERSION_STRING "${VERSION_VERSION}.${VERSION_MINOR}.${VERSION_PATCH}")
    set(MACOSX_BUNDLE_BUNDLE_NAME CryptonoteWallet)
    set(MACOSX_BUNDLE_SHORT_VERSION_STRING "${VERSION_VERSION}.${VERSION_MINOR}.${VERSION_PATCH}")
    set(MACOSX_BUNDLE_BUNDLE_VERSION "$${VERSION_VERSION}.${VERSION_MINOR}.${VERSION_PATCH}")

    find_package(Qt5PrintSupport REQUIRED)

    include_directories(/usr/include/malloc)

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -maes -std=c++11 -stdlib=libc++")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c11 -maes -D_DARWIN_C_SOURCE")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -framework Cocoa -framework OpenGL -framework CoreFoundation -framework Carbon -framework IOKit -L/usr/lib")

    set(MACOSX_BUNDLE_ICON_FILE cryptonote.icns)
    set(APPLICATION_ICON src/images/cryptonote.icns)
    set_source_files_properties(${APPLICATION_ICON} PROPERTIES MACOSX_PACKAGE_LOCATION "Resources")

    set(BUILD_PLATFORM MACOSX_BUNDLE)
    set(BUILD_RESOURCES ${APPLICATION_ICON})
	
    GET_TARGET_PROPERTY(QT_LIB_DIR "${Qt5Widgets_LIBRARIES}" LOCATION)
    GET_FILENAME_COMPONENT(QT_LIB_DIR "${QT_LIB_DIR}" PATH)
  else(APPLE)
    set(PLATFORM_DIR Linux)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -maes -std=c++11")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -maes -std=c11")
  endif (APPLE)
endif ()

include_directories(cryptocash/src/Platform/${PLATFORM_DIR})
file(GLOB PLATFORM_SOURCES cryptocash/src/Platform/${PLATFORM_DIR}/System/*)
set(CRYPTONOTE_SOURCES ${CRYPTONOTE_SOURCES} ${PLATFORM_SOURCES})

add_library(${CRYPTONOTE_LIB} STATIC ${CRYPTONOTE_SOURCES})
set_target_properties(${CRYPTONOTE_LIB} PROPERTIES COMPILE_DEFINITIONS _GNU_SOURCE)
target_link_libraries(${CRYPTONOTE_LIB} ${Boost_LIBRARIES})

add_executable(${PROJECT_NAME} ${BUILD_PLATFORM} ${BUILD_RESOURCES} ${SOURCES} ${HEADERS} ${UIS} ${RCC})
set_target_properties(${PROJECT_NAME} PROPERTIES COMPILE_DEFINITIONS _GNU_SOURCE)
target_link_libraries(${PROJECT_NAME} ${Boost_LIBRARIES} ${QTMAIN} ${CRYPTONOTE_LIB})

if (APPLE)
  qt5_use_modules(${PROJECT_NAME} PrintSupport)
elseif (UNIX)
  target_link_libraries(${PROJECT_NAME} -lpthread)
elseif (WIN32)
  target_link_libraries(${PROJECT_NAME} Imm32 Iphlpapi Winmm)
endif (APPLE)

qt5_use_modules(${PROJECT_NAME} Widgets Gui)
