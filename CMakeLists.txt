cmake_minimum_required(VERSION 3.0.2)

include(CryptoNoteWallet.cmake)

project(${CN_PROJECT_NAME})
# set(Boost_DEBUG ON)

set(VERSION_MAJOR 2)
set(VERSION_MINOR 1)
set(VERSION_PATCH 0)

set(CRYPTO_FOLDER "owndata")

if(WIN32)
	if(NOT MSVC)
		message(FATAL_ERROR "Only MSVC is supported on this platform")
	endif()

	set(CRYPTO_BUILD_DIR "build" CACHE STRING "Owndata Node Build directory")
	set(BOOST_ROOT "C:/Boost" CACHE STRING "Boost Directory")
	set(BOOST_LIBRARYDIR "${BOOST_ROOT}/lib-v141-x64/lib" CACHE STRING "Boost Library Directory")
	set(Boost_COMPILER "-vc141" CACHE STRING "Boost Compiler Used")
else()
	set(CRYPTO_BUILD_DIR "${CRYPTO_FOLDER}/build/" CACHE STRING "Owndata Node Build directory")
endif()

set(Boost_USE_STATIC_LIBS ON)
set(Boost_USE_STATIC_RUNTIME ON)

find_package(Git)
find_package(Boost 1.66 REQUIRED COMPONENTS
	filesystem
	date_time
	program_options
	regex
	serialization
	system
	thread
	chrono)

if(APPLE)
	#find_package(BoostPython REQUIRED)
endif()

find_package(Qt5Core REQUIRED)
find_package(Qt5Gui REQUIRED)
find_package(Qt5Widgets REQUIRED)
find_package(Qt5Network REQUIRED)
find_package(Qt5DBus REQUIRED)

set(GIT_REVISION "unknown")
if(GIT_FOUND)
    execute_process(
        COMMAND ${GIT_EXECUTABLE} rev-parse --short HEAD
        WORKING_DIRECTORY "${CMAKE_SOURCE_DIR}"
        OUTPUT_VARIABLE GIT_REVISION
        OUTPUT_STRIP_TRAILING_WHITESPACE
    )
    message(STATUS "Git Revision ${GIT_REVISION}")
else()
    message(STATUS "Git not found")
endif()

configure_file("${PROJECT_SOURCE_DIR}/src/CryptoNoteWalletConfig.h.in" "${PROJECT_BINARY_DIR}/CryptoNoteWalletConfig.h")

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(SOURCES
    src/CryptoNoteWrapper/InProcessNodeWorker.cpp
    src/Application/AddressBookManager.cpp
    src/Application/BlogReader.cpp
    src/Application/CommandLineParser.cpp
    src/Application/LogFileWatcher.cpp
    src/Application/MiningManager.cpp
    src/Application/OptimizationManager.cpp
    src/Application/SignalHandler.cpp
    src/Application/WalletApplication.cpp
    src/Application/WalletSplashScreen.cpp
    src/CryptoNoteWrapper/BlockChainExplorerAdapter.cpp
    src/CryptoNoteWrapper/BlockChainExplorerWorker.cpp
    src/CryptoNoteWrapper/CommonNodeAdapter.cpp
    src/CryptoNoteWrapper/CryptoNoteAdapter.cpp
    src/CryptoNoteWrapper/GuardExecutor.cpp
    src/CryptoNoteWrapper/InProcessNodeAdapter.cpp
    src/CryptoNoteWrapper/ProxyRpcNodeAdapter.cpp
    src/CryptoNoteWrapper/ProxyRpcNodeWorker.cpp
    src/CryptoNoteWrapper/SemaphoreUtils.cpp
    src/CryptoNoteWrapper/WalletGreenAdapter.cpp
    src/CryptoNoteWrapper/WalletGreenWorker.cpp
    src/Gui/AddressBook/AddressBookDelegate.cpp
    src/Gui/AddressBook/AddressBookFrame.cpp
    src/Gui/AddressBook/DonationColumnDelegate.cpp
    src/Gui/AddressBook/NewAddressDialog.cpp
    src/Gui/BlockchainExplorer/BlockDetailsDialog.cpp
    src/Gui/BlockchainExplorer/BlockExplorerFrame.cpp
    src/Gui/BlockchainExplorer/FilteredBlockModel.cpp
    src/Gui/BlockchainExplorer/TransactionsBlockModel.cpp
    src/Gui/BlockchainExplorer/WindowedBlockModel.cpp
    src/Gui/Common/AboutDialog.cpp
    src/Gui/Common/AddressBookDialog.cpp
    src/Gui/Common/BlockchainInstaller.cpp
    src/Gui/Common/ChangePasswordDialog.cpp
    src/Gui/Common/ClearableLineEdit.cpp
    src/Gui/Common/CopyColumnDelegate.cpp
    src/Gui/Common/CopyMagicLabel.cpp
    src/Gui/Common/ExitWidget.cpp
    src/Gui/Common/GlassFrame.cpp
    src/Gui/Common/KeyDialog.cpp
    src/Gui/Common/LinkLikeColumnDelegate.cpp
    src/Gui/Common/MagicLabel.cpp
    src/Gui/Common/NewPasswordDialog.cpp
    src/Gui/Common/NewsFrame.cpp
    src/Gui/Common/NewTransactionDelegate.cpp
    src/Gui/Common/P2pBindPortErrorDialog.cpp
    src/Gui/Common/PoolTransactionDetailsDialog.cpp
    src/Gui/Common/QuestionDialog.cpp
    src/Gui/Common/ResizableScrollArea.cpp
    src/Gui/Common/RightAlignmentColumnDelegate.cpp
    src/Gui/Common/TransactionDetailsDialog.cpp
    src/Gui/Common/TransactionsAmountDelegate.cpp
    src/Gui/Common/TransactionsHeaderView.cpp
    src/Gui/Common/TransactionsTimeDelegate.cpp
    src/Gui/Common/TransfersHeaderView.cpp
    src/Gui/Common/WalletBlueButton.cpp
    src/Gui/Common/WalletCancelButton.cpp
    src/Gui/Common/WalletDescriptionLabel.cpp
    src/Gui/Common/WalletGrayCheckBox.cpp
    src/Gui/Common/WalletLinkLikeButton.cpp
    src/Gui/Common/WalletNavigationButton.cpp
    src/Gui/Common/WalletOkButton.cpp
    src/Gui/Common/WalletTableView.cpp
    src/Gui/Common/WalletTextLabel.cpp
    src/Gui/Common/WalletTreeView.cpp
    src/Gui/Common/WalletWindowedItemModel.cpp
    src/Gui/MainWindow/MainWindow.cpp
    src/Gui/MainWindow/WalletStatusBar.cpp
    src/Gui/Mining/MinerDelegate.cpp
    src/Gui/Mining/MiningFrame.cpp
    src/Gui/Mining/PoolHeaderView.cpp
    src/Gui/Mining/PoolTreeView.cpp
    src/Gui/NoWallet/NoWalletFrame.cpp
    src/Gui/Options/ConnectionOptionsFrame.cpp
    src/Gui/Options/DonationAddressesModel.cpp
    src/Gui/Options/DonationOptionsFrame.cpp
    src/Gui/Options/OptimizationOptionsFrame.cpp
    src/Gui/Options/OptionsDialog.cpp
    src/Gui/Options/PrivacyFrame.cpp
    src/Gui/Overview/OverviewFrame.cpp
    src/Gui/Overview/OverviewHeaderFrame.cpp
    src/Gui/Overview/OverviewHeaderGlassFrame.cpp
    src/Gui/Overview/OverviewTransactionPoolModel.cpp
    src/Gui/Overview/OverviewTransactionPoolTreeView.cpp
    src/Gui/Overview/RecentTransactionsModel.cpp
    src/Gui/Send/SendFrame.cpp
    src/Gui/Send/SendGlassFrame.cpp
    src/Gui/Send/TransferFrame.cpp
    src/Gui/Transactions/DrawableTransfersModel.cpp
    src/Gui/Transactions/FilteredByAddressTransactionsModel.cpp
    src/Gui/Transactions/FilteredByAgeTransactionsModel.cpp
    src/Gui/Transactions/FilteredByHashTransactionsModel.cpp
    src/Gui/Transactions/FilteredByPeriodTransactionsModel.cpp
    src/Gui/Transactions/FilteredTransactionsModel.cpp
    src/Gui/Transactions/TransactionsDelegate.cpp
    src/Gui/Transactions/TransactionsFrame.cpp
    src/Gui/Transactions/TransactionTransfersRenderer.cpp
    src/main.cpp
    src/Miner/Miner.cpp
    src/Miner/StratumClient.cpp
    src/Miner/Worker.cpp
    src/Models/AddressBookModel.cpp
    src/Models/BlockchainModel.cpp
    src/Models/FusionTransactionsFilterModel.cpp
    src/Models/MinerModel.cpp
    src/Models/NodeStateModel.cpp
    src/Models/SortedAddressBookModel.cpp
    src/Models/SortedTransactionsModel.cpp
    src/Models/TransactionPoolModel.cpp
    src/Models/TransactionsModel.cpp
    src/Models/TransfersModel.cpp
    src/Models/WalletStateModel.cpp
    src/QJsonRpc/JsonRpcNotification.cpp
    src/QJsonRpc/JsonRpcObject.cpp
    src/QJsonRpc/JsonRpcObjectFactory.cpp
    src/QJsonRpc/JsonRpcRequest.cpp
    src/QJsonRpc/JsonRpcResponse.cpp
    src/QJsonRpc/JsonRpcServer.cpp
    src/Settings/Settings.cpp
    src/Style/DarkStyle.cpp
    src/Style/LightStyle.cpp
    src/Style/Style.cpp
    src/WalletLogger/WalletLogger.cpp)

if(WIN32)
	set(SOURCES ${SOURCES} src/Platform/Windows/ApplicationEventHandler.cpp)
	add_definitions(/D_CRT_SECURE_NO_WARNINGS /D_WIN32_WINNT=0x0600)
	include_directories(
		${CRYPTO_FOLDER}/src/Platform/msc
		${CRYPTO_FOLDER}/src/Platform/Windows
		src/Platform/Windows)
		
	set(BUILD_PLATFORM WIN32)
	
	link_directories(
		${Boost_LIBRARY_DIRS}
		${CRYPTO_BUILD_DIR}/src
		${CRYPTO_BUILD_DIR}/external/rocksdb
		${CRYPTO_BUILD_DIR}/external/miniupnpc)
else()
    file (GLOB_RECURSE OBJC_SOURCES "owndata/src/System/*" )
    set(SOURCES ${OBJC_SOURCES} ${SOURCES} )

    file (GLOB_RECURSE OBJC_SOURCES "src/Platform/OSX/*" )
    set(SOURCES ${OBJC_SOURCES} ${SOURCES} )

    file (GLOB_RECURSE OBJC_SOURCES "owndata/src/Platform/OSX/*" )
    set(SOURCES ${OBJC_SOURCES} ${SOURCES} )

    file (GLOB_RECURSE OBJC_SOURCES "owndata/src/Platform/Posix/*" )
    set(SOURCES ${OBJC_SOURCES} ${SOURCES} )

    file (GLOB_RECURSE OBJC_SOURCES "owndata/src/System/*.h" )
    set(SOURCES ${OBJC_SOURCES} ${SOURCES} )

	set(MACOSX_BUNDLE_ICON_FILE owndata.icns)
    set(APPLICATION_ICON src/images/owndata.icns)
    set_source_files_properties(${APPLICATION_ICON} PROPERTIES MACOSX_PACKAGE_LOCATION "Resources")
	
	include_directories(BEFORE 
		"${CMAKE_SOURCE_DIR}/${CRYPTO_FOLDER}/src/Platform/Posix"
		"${CMAKE_SOURCE_DIR}/${CRYPTO_FOLDER}/src/Platform/OSX"
		"${CMAKE_SOURCE_DIR}/src/Platform/OSX")

	set(BUILD_PLATFORM MACOSX_BUNDLE)
	
	include_directories(${Boost_INCLUDE_DIRS})
	
	set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -framework Cocoa -framework OpenGL -framework CoreFoundation -framework Foundation -framework Carbon -framework IOKit -L/usr/lib")
	
	link_directories(
		${Boost_LIBRARY_DIRS}
		${CRYPTO_FOLDER}/build/src
		${CRYPTO_FOLDER}/external/rocksdb/build
		${CRYPTO_FOLDER}/external/miniupnpc/build)

	GET_DIRECTORY_PROPERTY(INCLUDE_DIRECTORIES_LIST INCLUDE_DIRECTORIES) 
endif()

add_executable(${PROJECT_NAME} ${BUILD_PLATFORM} ${SOURCES} src/resources.qrc src/owndatawallet.rc ${APPLICATION_ICON} )
set_target_properties(${PROJECT_NAME} PROPERTIES COMPILE_DEFINITIONS _GNU_SOURCE)
target_compile_definitions(${PROJECT_NAME} PUBLIC -DGIT_REVISION="${GIT_REVISION}")


if(WIN32)
	set(WIN32LIBS Imm32 Iphlpapi Rpcrt4 Winmm Wtsapi32)
	set(PLATFORM_SPEC_LIB Qt5::WinMain rocksdblib)
else()
	file(GLOB_RECURSE CRYPTO_LIBRARIES "${CRYPTO_FOLDER}/build/src/*.a" )
    file(GLOB_RECURSE EXTERNAL_CN_LIBRARIES "${CRYPTO_FOLDER}/external/*.a" )
	set(PLATFORM_SPEC_LIB ${Boost_LIBRARIES})
	set(Boost_INCLUDE_DIR ${Boost_INCLUDE_DIR}/boost)
endif()

target_include_directories(${PROJECT_NAME} PUBLIC
	include
	src
	${CRYPTO_FOLDER}/include
	${CRYPTO_FOLDER}/src
	${CRYPTO_FOLDER}/external/rocksdb/include
	${Boost_INCLUDE_DIR})

target_link_libraries(${PROJECT_NAME}
	${CRYPTO_LIBRARIES}
	${Qt5DBus_LIBRARIES}
	${EXTERNAL_CN_LIBRARIES}
    ${PLATFORM_SPEC_LIB}
	BlockchainExplorer
	Common
	Crypto
	CryptoNoteCore
	Http
	InProcessNode
	Logging
	NodeRpcProxy
	P2p
	Rpc
	Serialization
	System
	Transfers
	Wallet
	miniupnpc
	Qt5::Widgets
	Qt5::Gui
	Qt5::Network
	${WIN32LIBS}) 
