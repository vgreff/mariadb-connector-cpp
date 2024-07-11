# ************************************************************************************
#   Copyright (C) 2013,2023 MariaDB Corporation AB
#
#   This library is free software; you can redistribute it and/or
#   modify it under the terms of the GNU Library General Public
#   License as published by the Free Software Foundation; either
#   version 2.1 of the License, or (at your option) any later version.
#
#   This library is distributed in the hope that it will be useful,
#   but WITHOUT ANY WARRANTY; without even the implied warranty of
#   MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
#   Library General Public License for more details.
#
#   You should have received a copy of the GNU Library General Public
#   License along with this library; if not see <http://www.gnu.org/licenses>
#   or write to the Free Software Foundation, Inc.,
#   51 Franklin St., Fifth Floor, Boston, MA 02110, USA
# *************************************************************************************/

IF(APPLE)
  # productbuild packaging stuff 
  CMAKE_MINIMUM_REQUIRED(VERSION 3.23)
ELSE()
  CMAKE_MINIMUM_REQUIRED(VERSION 3.1)
ENDIF()
CMAKE_POLICY(SET CMP0048 NEW)

PROJECT(mariadb_connector_cpp
        VERSION 1.0.4
#        DESCRIPTION "MariaDB Connector/C++"
        LANGUAGES CXX C)

IF(CMAKE_COMPILER_IS_GNUCC AND CMAKE_CXX_COMPILER_VERSION VERSION_LESS 4.8)
  MESSAGE(FATAL_ERROR "GCC version >=4.8 is required")
ENDIF()

SET(MACPP_VERSION_QUALITY "ga") #Empty also means GA
SET(MACPP_VERSION "1.00.0004")
SET(MARIADB_DEFAULT_PLUGINS_SUBDIR "plugin")

# For C/C
IF(CMAKE_VERSION VERSION_LESS "3.1")
  IF(CMAKE_C_COMPILER_ID STREQUAL "GNU")
    SET (CMAKE_C_FLAGS ${CMAKE_C_FLAGS} "-std=gnu99 ${CMAKE_C_FLAGS}")
  ENDIF()
ELSE()
  SET(CMAKE_C_STANDARD 99)
ENDIF()

SET(CMAKE_CXX_STANDARD 11)
SET(CMAKE_CXX_STANDARD_REQUIRED ON)

GET_PROPERTY(MAODBC_LANGUAGES GLOBAL PROPERTY ENABLED_LANGUAGES)
# We don't need RC for what we need MAODBC_LANGUAGES for
LIST(REMOVE_ITEM MAODBC_LANGUAGES "RC")

SET(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

SET(LIBRARY_NAME "mariadbcpp")
SET(STATIC_LIBRARY_NAME "${LIBRARY_NAME}-static")

CONFIGURE_FILE(${CMAKE_CURRENT_SOURCE_DIR}/src/Version.h.in
               ${CMAKE_CURRENT_SOURCE_DIR}/src/Version.h)
CONFIGURE_FILE(${CMAKE_CURRENT_SOURCE_DIR}/src/maconncpp.rc.in
               ${CMAKE_CURRENT_SOURCE_DIR}/src/maconncpp.rc)
INCLUDE(SearchLibrary)
INCLUDE(SetValueMacro)


SET (MACPP_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbDriver.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/DriverManager.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/UrlParser.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbDatabaseMetaData.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/HostAddress.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/Consts.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/SQLString.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbConnection.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbStatement.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDBException.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDBWarning.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/Identifier.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbSavepoint.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/SqlStates.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/Results.cpp

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/ColumnType.cpp

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/ColumnDefinition.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/protocol/MasterProtocol.cpp

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/protocol/capi/QueryProtocol.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/protocol/capi/ConnectProtocol.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/capi/ColumnDefinitionCapi.cpp

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/cache/CallableStatementCache.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/cache/CallableStatementCacheKey.cpp

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/Value.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/Utils.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/String.cpp

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/logger/NoLogger.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/logger/LoggerFactory.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/logger/ProtocolLoggingProxy.cpp

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/ParameterHolder.cpp

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/options/Options.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/options/DefaultOptions.cpp

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/pool/GlobalStateInfo.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/pool/Pools.cpp

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/failover/FailoverProxy.cpp

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/credential/CredentialPluginLoader.cpp

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/SelectResultSet.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/capi/SelectResultSetCapi.cpp
                   #src/com/SelectResultSetPacket.cpp

                   #src/com/ColumnDefinitionPacket.cpp

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/ColumnNameMap.cpp

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/io/StandardPacketInputStream.cpp

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/Charset.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/ClientSidePreparedStatement.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/BasePrepareStatement.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbFunctionStatement.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbProcedureStatement.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/ServerSidePreparedStatement.cpp

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/BigDecimalParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/BooleanParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/ByteArrayParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/ByteParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/DateParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/DefaultParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/DoubleParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/FloatParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/IntParameter.cpp
                   #src/parameters/LocalTimeParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/LongParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/ULongParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/NullParameter.cpp
                   #src/parameters/OffsetTimeParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/ParameterHolder.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/ReaderParameter.cpp
                   #src/parameters/SerializableParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/ShortParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/StreamParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/TimeParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/TimestampParameter.cpp
                   #src/parameters/ZonedDateTimeParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/StringParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbPooledConnection.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbParameterMetaData.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbResultSetMetaData.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/CallParameter.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/CallableParameterMetaData.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/Packet.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/credential/Credential.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/LogQueryTool.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/ClientPrepareResult.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/ServerPrepareResult.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/ServerPrepareStatementCache.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/CmdInformationSingle.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/CmdInformationBatch.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/CmdInformationMultiple.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/RowProtocol.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/protocol/capi/BinRowProtocolCapi.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/protocol/capi/TextRowProtocolCapi.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/ExceptionFactory.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/StringImp.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/CArray.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/SimpleParameterMetaData.cpp
                   ####CPP####
                   )

IF(WIN32)
  SET(MACPP_SOURCES ${MACPP_SOURCES}
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/Dll.c

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDBConnCpp.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbDriver.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/UrlParser.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbDatabaseMetaData.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/HostAddress.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/Version.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/Consts.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbConnection.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbStatement.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDBWarning.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/Protocol.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/Identifier.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbSavepoint.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/SqlStates.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/Results.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/ColumnDefinition.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbServerCapabilities.h

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/protocol/MasterProtocol.h

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/protocol/capi/QueryProtocol.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/protocol/capi/ConnectProtocol.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/capi/ColumnDefinitionCapi.h

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/cache/CallableStatementCache.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/cache/CallableStatementCacheKey.h

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/Value.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/ClassField.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/Utils.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/StateChange.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/String.h

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/logger/NoLogger.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/logger/LoggerFactory.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/logger/Logger.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/logger/ProtocolLoggingProxy.h

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/ParameterHolder.h

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/options/Options.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/options/DefaultOptions.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/options/DriverPropertyInfo.h

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/io/PacketOutputStream.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/io/PacketInputStream.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/io/StandardPacketInputStream.h

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/credential/CredentialPlugin.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/credential/CredentialPluginLoader.h

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/pool/GlobalStateInfo.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/pool/Pools.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/pool/Pool.h

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/failover/FailoverProxy.h

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/Listener.h

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/CmdInformation.h

                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/Connection.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/Statement.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/ResultSet.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/PreparedStatement.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/ParameterMetaData.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/ResultSetMetaData.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/DatabaseMetaData.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/CallableStatement.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/SQLString.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/Warning.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/Exception.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/jdbccompat.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/Driver.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/DriverManager.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/Types.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/buildconf.hpp"
                   "${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp/CArray.hpp"

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/SelectResultSet.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/capi/SelectResultSetCapi.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/SelectResultSetPacket.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/ColumnType.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/ColumnDefinitionPacket.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/ColumnNameMap.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/Charset.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/ClientSidePreparedStatement.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/BasePrepareStatement.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbFunctionStatement.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbProcedureStatement.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/ServerSidePreparedStatement.h

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/BigDecimalParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/BooleanParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/ByteArrayParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/ByteParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/DateParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/DefaultParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/DoubleParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/FloatParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/IntParameter.h
                   #src/parameters/LocalTimeParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/LongParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/ULongParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/NullParameter.h
                   #src/parameters/OffsetTimeParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/ReaderParameter.h
                   #src/parameters/SerializableParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/ShortParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/StreamParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/TimeParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/TimestampParameter.h
                   #src/parameters/ZonedDateTimeParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/parameters/StringParameter.h

                   ${CMAKE_CURRENT_SOURCE_DIR}/src/Parameters.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbPooledConnection.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbParameterMetaData.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/CallParameter.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/CallableParameterMetaData.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/MariaDbResultSetMetaData.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/Packet.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/ServerStatus.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/credential/Credential.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/LogQueryTool.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/PrepareResult.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/ClientPrepareResult.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/ServerPrepareResult.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/util/ServerPrepareStatementCache.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/CmdInformationSingle.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/CmdInformationBatch.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/CmdInformationMultiple.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/com/RowProtocol.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/protocol/capi/BinRowProtocolCapi.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/protocol/capi/TextRowProtocolCapi.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/ExceptionFactory.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/StringImp.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/CArrayImp.h
                   ${CMAKE_CURRENT_SOURCE_DIR}/src/SimpleParameterMetaData.h
                   ####H####
                       )
  SOURCE_GROUP(Logging REGULAR_EXPRESSION "/logger/")
  SOURCE_GROUP(PublicAPI REGULAR_EXPRESSION "include/")
  SOURCE_GROUP(Parameters REGULAR_EXPRESSION "src/parameters")
  SOURCE_GROUP(Options REGULAR_EXPRESSION "src/options")
  SOURCE_GROUP(IO REGULAR_EXPRESSION "src/io")
  SOURCE_GROUP(Credential REGULAR_EXPRESSION "src/credential")
  SOURCE_GROUP(Pool REGULAR_EXPRESSION "src/pool")
  SOURCE_GROUP(Utils REGULAR_EXPRESSION "src/util")
  SOURCE_GROUP(Caches REGULAR_EXPRESSION "src/cache")
  SOURCE_GROUP(Failover REGULAR_EXPRESSION "src/failover")
  SOURCE_GROUP(Commands REGULAR_EXPRESSION "src/com/")
  SOURCE_GROUP(Protocol REGULAR_EXPRESSION "src/protocol/")
  SOURCE_GROUP(C_API REGULAR_EXPRESSION "/capi/")

  SET(PLATFORM_DEPENDENCIES ws2_32)
ELSE()
  SEARCH_LIBRARY(LIB_MATH floor m)
  MESSAGE(STATUS "Found math lib: ${LIB_MATH}")
  SET(PLATFORM_DEPENDENCIES ${LIB_MATH})
ENDIF()

INCLUDE(check_compiler_flag)
IF(WITH_ASAN)
  IF(MSVC)
    MA_SET_COMPILER_FLAG("-fsanitize=address" DEBUG RELWITHDEBINFO)
    SET(MAODBC_LINKER_FLAGS ${MAODBC_LINKER_FLAGS} /INCREMENTAL:NO)
    MA_SET_LINKER_FLAG("${MAODBC_LINKER_FLAGS}" DEBUG RELWITHDEBINFO)
  ELSE()
    MY_CHECK_AND_SET_COMPILER_FLAG("-U_FORTIFY_SOURCE" DEBUG RELWITHDEBINFO)
    MY_CHECK_AND_SET_COMPILER_FLAG("-fsanitize=address -fPIC" DEBUG RELWITHDEBINFO)

    SET(WITH_ASAN_OK 1)
    FOREACH(lang ${MAODBC_LANGUAGES})
      IF(NOT ${have_${lang}__fsanitize_address__fPIC})
        SET(WITH_ASAN_OK 0)
      ENDIF()
    ENDFOREACH()
    IF(WITH_ASAN_OK)
      OPTION(WITH_ASAN_SCOPE "Enable -fsanitize-address-use-after-scope" OFF)
      IF(WITH_ASAN_SCOPE)
        MY_CHECK_AND_SET_COMPILER_FLAG("-fsanitize=address -fsanitize-address-use-after-scope" DEBUG RELWITHDEBINFO)
      ENDIF()
    ELSE()
      MESSAGE(FATAL_ERROR "Do not know how to enable address sanitizer")
    ENDIF()
  ENDIF()
ENDIF()

IF (WITH_UBSAN)
  MY_CHECK_AND_SET_COMPILER_FLAG("-fsanitize=undefined -fno-sanitize=alignment -U_FORTIFY_SOURCE -DWITH_UBSAN" DEBUG RELWITHDEBINFO)
ENDIF()

IF (WITH_MSAN)
  MY_CHECK_AND_SET_COMPILER_FLAG("-fsanitize=memory -fsanitize-memory-track-origins -U_FORTIFY_SOURCE" DEBUG RELWITHDEBINFO)
ENDIF()

# This has to be before C/C's cmake run, or it will build with /MD
IF(WIN32)
  IF (MSVC)
    SET(CONFIG_TYPES "DEBUG" "RELEASE" "RELWITHDEBINFO" "MINSIZEREL")
    FOREACH(BUILD_TYPE ${CONFIG_TYPES})
      FOREACH(COMPILER ${MAODBC_LANGUAGES})
        SET(COMPILER_FLAGS "${CMAKE_${COMPILER}_FLAGS_${BUILD_TYPE}}")
        IF (NOT COMPILER_FLAGS STREQUAL "")
          IF(NOT WITH_ASAN)
            STRING(REPLACE "/MD" "/MT" COMPILER_FLAGS ${COMPILER_FLAGS})
            STRING(REPLACE "/Zi" "/ZI" COMPILER_FLAGS ${COMPILER_FLAGS})
          ENDIF(NOT WITH_ASAN)
          MESSAGE (STATUS "CMAKE_${COMPILER}_FLAGS_${BUILD_TYPE}= ${COMPILER_FLAGS}")
          SET(CMAKE_${COMPILER}_FLAGS_${BUILD_TYPE} ${COMPILER_FLAGS} CACHE
               STRING "Overwritten by mariadb-cpp" FORCE)
        ENDIF()
      ENDFOREACH()
    ENDFOREACH()
  ENDIF()
  ADD_DEFINITIONS(-D_CRT_SECURE_NO_WARNINGS -D_SCL_SECURE_NO_WARNINGS -DWIN32_LEAN_AND_MEAN)
  SET(INSTALL_PLUGINDIR "${MARIADB_DEFAULT_PLUGINS_SUBDIR}")
  SET(PLATFORM_DEPENDENCIES ${PLATFORM_DEPENDENCIES} version.lib)
ENDIF()

### Build options, initial settings and platform defaults
INCLUDE("options_defaults")

### Setting installation paths - should go before C/C subproject sets its own. We need to have control over those
INCLUDE("install")

IF(NOT MINGW AND WIN32)
  MESSAGE(STATUS "Generate MSI package: ${WITH_MSI}")
  MESSAGE(STATUS "Sign MSI package: ${WITH_SIGNCODE}")
ENDIF()

# By now we have everything needed by tests. If we need to build them only - firing config now and exit
# There is "normal" tests config below
IF(BUILD_TESTS_ONLY)
  IF(WIN32)
    LINK_DIRECTORIES("$ENV{ProgramFiles}/MariaDB/MariaDB C++ Connector 64-bit")
  ELSE()
    MESSAGE(STATUS "Configurig Tests: tcp://${TEST_UID}@${TEST_SERVER}:${TEST_PORT}/${TEST_SCHEMA} socket: ${TEST_SOCKET}")
    SET(DRIVER_LIB_LOCATION "${libmariadb_prefix}/${INSTALL_LIBDIR}")
  ENDIF()
  INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/include)
  INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp)
  # At some point use of String.cpp from the driver was added to tests. Need to verify if it's still needed/a good idea.
  INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/src)
  ADD_SUBDIRECTORY(test)
  # Only means only
  RETURN()
ENDIF()

ADD_CUSTOM_TARGET(DEPENDENCIES_FOR_PACKAGE)
IF(NOT WITH_SSL)
  MESSAGE(STATUS "Setting default value for WITH_SSL for libmariadb build to ON")
  SET(CONC_WITH_SSL ON)
ENDIF()

### Including C/C subproject
IF(NOT USE_SYSTEM_INSTALLED_LIB)
  IF(EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/libmariadb)
    IF(GIT_BUILD_SRCPKG)
      # We don't want conn/c (wrong) src pkg to be built.
      SET(GIT_BUILD_SRCPKG FALSE)
      SET(CONNCPP_GIT_BUILD_SRCPKG TRUE)
    ENDIF()
    MESSAGE(STATUS "Running C/C cmake scripts")
    INCLUDE(connector_c)
    INSTALL(FILES
            $<TARGET_FILE:libmariadb>
            DESTINATION ${INSTALL_LIBDIR}
            COMPONENT ConCLib)
    MESSAGE(STATUS "Configuring to install libmariadb to ${INSTALL_LIBDIR}")

    SET(OWN_PLUGINS_LIST mysql_clear_password dialog client_ed25519 sha256_password caching_sha2_password)
    IF (PLUGINS_DYNAMIC)
      # The list from CC is visible for us
      SET(PLUGINS_LIST ${PLUGINS_DYNAMIC})
    ELSE()
      SET(PLUGINS_LIST ${OWN_PLUGINS_LIST})
    ENDIF()
    FOREACH(CC_PLUGIN ${PLUGINS_LIST})
      IF(NOT PLUGINS_DYNAMIC OR "${PLUGIN_${CC_PLUGIN}_TYPE}" STREQUAL "MARIADB_CLIENT_PLUGIN_AUTH")
        MESSAGE(STATUS "Configuring to install ${CC_PLUGIN} to ${INSTALL_PLUGINDIR}")
        ADD_DEPENDENCIES(DEPENDENCIES_FOR_PACKAGE ${CC_PLUGIN})
        INSTALL(FILES
                $<TARGET_FILE:${CC_PLUGIN}>
                DESTINATION ${INSTALL_PLUGINDIR}
                COMPONENT ConCPlugins)
      ENDIF()
    ENDFOREACH()
    INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/libmariadb/include)
    INCLUDE_DIRECTORIES(${CMAKE_BINARY_DIR}/libmariadb/include)
  ELSE()
    SET(USE_SYSTEM_INSTALLED_LIB TRUE)
    IF (NOT WIN32)
      INCLUDE_DIRECTORIES(/usr/include/mariadb)
      # /usr/${INSTALL_LIBDIR}
    ENDIF()
    MESSAGE(STATUS "There is no Connector/C sub-project folder, linking against libmariadb installed on the system")
  ENDIF()
ENDIF()

IF(WITH_MSI AND WITH_SIGNCODE)
  IF(WIN32 AND NOT SIGN_OPTIONS)
    SET(SIGN_OPTIONS /a /t http://timestamp.verisign.com/scripts/timstamp.dll)
  ELSE()
    SEPARATE_ARGUMENTS(SIGN_OPTIONS)
  ENDIF()
  MARK_AS_ADVANCED(SIGN_OPTIONS)
ENDIF()

INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/include)
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/include/conncpp)
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/src)
IF(NOT CMAKE_BUILD_TYPE)
  SET(CMAKE_BUILD_TYPE "RelWithDebInfo")
ENDIF()
CONFIGURE_FILE(${CMAKE_CURRENT_SOURCE_DIR}/src/maconncpp.def.in
               ${CMAKE_CURRENT_SOURCE_DIR}/src/maconncpp.def)

# Dynamic linking is default on non-Windows
IF(MARIADB_LINK_DYNAMIC)
  IF(USE_SYSTEM_INSTALLED_LIB)
    IF(MINGW)
      # I guess -l can be removed here. Also, for build with c/c as submodule this will have to me moved on top level out of this IF's 
      SET(PLATFORM_DEPENDENCIES "${PLATFORM_DEPENDENCIES}" "-lsecur32 -lcrypt32")
      # Not sure if mingw really needs this separate mysql.h search, but let it be
      FIND_PATH(MARIADB_CONNECTORC_INCLUDE NAMES "mysql.h"
                DOC "Path to MariaDB Connector/C Include Directory")

      FIND_PATH(MARIADB_CONNECTORC_LIB NAMES libmariadbclient.a
                                             liblibmariadb.dll.a 
                                             libmariadb.dll 
                                             mariadbclient
                                             mariadb
                DOC "Path to MariaDB Connector/C Lib Directory")
      # Not sure why is shlwapi is needed
      FIND_LIBRARY(MARIADB_SHLWAPI_LIB NAMES ShLwApi.Lib 
                                             ShLwApi
                DOC "Path to ShLwApi.Lib. Usually found in $ENV{programfiles\(x86\)}\\Windows Kits\\10\\Lib")
    ELSE()
      FIND_LIBRARY(CCLIB libmariadb.so)
      IF (${CCLIB} STREQUAL "CCLIB-NOTFOUND")
        # It won't be found by linker either unless user does some magic before build, so we actually could stop here with error
        MESSAGE(STATUS "C/C library has not been found")
        SET(MARIADB_CLIENT_TARGET_NAME mariadb)
      ELSE()
        MESSAGE(STATUS "C/C library found here ${CCLIB}")
        SET(MARIADB_CLIENT_TARGET_NAME ${CCLIB})
      ENDIF()
      FIND_FILE(CCHEADER NAMES "mysql.h"
                PATHS /usr/include/mariadb
                      /usr/include/mysql
                      /usr/local/include/mariadb
                      /usr/local/include/mysql)
                #PATH_SUFFIXES mariadb mysql)
      IF (${CCHEADER} STREQUAL "CCHEADER-NOTFOUND")
        # Again should probably stop
        MESSAGE(STATUS "C/C headers have not been found")
        INCLUDE_DIRECTORIES(/usr/include/mariadb /usr/include/mysql)
      ELSE()
        GET_FILENAME_COMPONENT(CCHEADERDIR ${CCHEADER} DIRECTORY)
        MESSAGE(STATUS "C/C headers have been found here ${CCHEADERDIR}")
        INCLUDE_DIRECTORIES(${CCHEADERDIR})
      ENDIF()
    ENDIF()
  ELSE()
    SET(MARIADB_CLIENT_TARGET_NAME libmariadb)
  ENDIF()
  MESSAGE(STATUS "Linking Connector/C library dynamically(${MARIADB_CLIENT_TARGET_NAME})")
ELSE()
  SET(MARIADB_CLIENT_TARGET_NAME mariadbclient)
  MESSAGE(STATUS "Linking Connector/C library statically(${MARIADB_CLIENT_TARGET_NAME})")
ENDIF()


ADD_LIBRARY(${LIBRARY_NAME}_obj OBJECT ${MACPP_SOURCES})
IF(UNIX)
  SET_TARGET_PROPERTIES(${LIBRARY_NAME}_obj PROPERTIES COMPILE_FLAGS "${CMAKE_SHARED_LIBRARY_C_FLAGS}")
ENDIF()
SET(${LIBRARY_NAME}_OBJECTS $<TARGET_OBJECTS:${LIBRARY_NAME}_obj>)

IF(WIN32 AND NOT MINGW)
  ADD_LIBRARY(${LIBRARY_NAME} SHARED ${CMAKE_CURRENT_SOURCE_DIR}/src/maconncpp.rc ${${LIBRARY_NAME}_OBJECTS} ${CMAKE_CURRENT_SOURCE_DIR}/src/maconncpp.def)
  ADD_LIBRARY(${STATIC_LIBRARY_NAME} STATIC ${${LIBRARY_NAME}_OBJECTS} ${CMAKE_CURRENT_SOURCE_DIR}/src/maconncpp.rc)

  TARGET_COMPILE_DEFINITIONS(${LIBRARY_NAME}_obj PRIVATE "MARIADB_EXPORTED=__declspec(dllexport)")

  TARGET_COMPILE_DEFINITIONS(${LIBRARY_NAME} PRIVATE "MARIADB_EXPORTED=__declspec(dllexport)")
  TARGET_COMPILE_DEFINITIONS(${LIBRARY_NAME} INTERFACE "MARIADB_EXPORTED=__declspec(dllimport)")

  TARGET_COMPILE_DEFINITIONS(${STATIC_LIBRARY_NAME} PUBLIC MARIADB_STATIC_LINK)
ELSE()
  # Xcode doesn't support targets that have only object files,
  # so let's add an empty file to keep Xcode happy
  IF(CMAKE_GENERATOR MATCHES Xcode)
    FILE(WRITE ${CMAKE_BINARY_DIR}/empty.cpp "")
    SET(EMPTY_FILE ${CMAKE_BINARY_DIR}/empty.cpp)
  ENDIF()
  #  MESSAGE(STATUS "Version script: ${CMAKE_CURRENT_SOURCE_DIR}/src/maconncpp.def")
  ADD_LIBRARY(${LIBRARY_NAME} SHARED ${${LIBRARY_NAME}_OBJECTS} ${EMPTY_FILE})
  ADD_LIBRARY(${STATIC_LIBRARY_NAME} STATIC ${${LIBRARY_NAME}_OBJECTS} ${EMPTY_FILE})

  IF(APPLE)
    SET_TARGET_PROPERTIES(${LIBRARY_NAME} PROPERTIES LINK_FLAGS "-Wl"
                                     INSTALL_RPATH_USE_LINK_PATH 0
                                     BUILD_WITH_INSTALL_RPATH 1
                                     XCODE_ATTRIBUTE_ENABLE_HARDENED_RUNTIME YES
                                     XCODE_ATTRIBUTE_OTHER_CODE_SIGN_FLAGS "--timestamp -f"
                                     INSTALL_RPATH "${MAODBC_INSTALL_RPATH}")
    SET_TARGET_PROPERTIES(${STATIC_LIBRARY_NAME} PROPERTIES 
                                     INSTALL_RPATH_USE_LINK_PATH 0
                                     BUILD_WITH_INSTALL_RPATH 1
                                     XCODE_ATTRIBUTE_ENABLE_HARDENED_RUNTIME YES
                                     XCODE_ATTRIBUTE_OTHER_CODE_SIGN_FLAGS "--timestamp -f"
                                     INSTALL_RPATH "${MAODBC_INSTALL_RPATH}")
    IF(WITH_SIGNCODE)
      SET_TARGET_PROPERTIES(${LIBRARY_NAME} PROPERTIES XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY "Developer ID Application: ${DEVELOPER_ID}")
      SET_TARGET_PROPERTIES(${STATIC_LIBRARY_NAME} PROPERTIES XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY "Developer ID Application: ${DEVELOPER_ID}")
    ELSE()
      SET_TARGET_PROPERTIES(${LIBRARY_NAME} PROPERTIES XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY "")
      SET_TARGET_PROPERTIES(${STATIC_LIBRARY_NAME} PROPERTIES XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY "")
    ENDIF()
  ELSE()
#    SET_TARGET_PROPERTIES(${LIBRARY_NAME} PROPERTIES LINK_FLAGS "-Wl,--version-script=${CMAKE_CURRENT_SOURCE_DIR}/src/maconncpp.def")
  ENDIF()
ENDIF()

TARGET_LINK_LIBRARIES(${LIBRARY_NAME} ${MARIADB_CLIENT_TARGET_NAME} ${PLATFORM_DEPENDENCIES})
TARGET_LINK_LIBRARIES(${STATIC_LIBRARY_NAME} mariadbclient)

# MINGW Specific includes and linking
IF(MINGW)
  
  IF(MARIADB_SHLWAPI_LIB)
    TARGET_LINK_LIBRARIES(${LIBRARY_NAME} ${MARIADB_SHLWAPI_LIB})
    MESSAGE(STATUS "Found shlwapi lib - ${MARIADB_SHLWAPI_LIB}")
  ELSE()
    # Should here and below be just warnings?
    MESSAGE(WARNING "Did not find shlwapi lib")
  ENDIF()

  IF(MARIADB_CONNECTORC_INCLUDE)
    # Probably has to be private, and not PUBLIC here
    TARGET_INCLUDE_DIRECTORIES(${LIBRARY_NAME}_obj PUBLIC  ${MARIADB_CONNECTORC_INCLUDE})
    MESSAGE(STATUS "Found MariaDB Connector/C includes - ${MARIADB_CONNECTORC_INCLUDE}")
  ELSE()
    MESSAGE(WARNING "Did not find MariaDB Connector/C includes")
  ENDIF()
  
  IF(MARIADB_CONNECTORC_LIB)
    TARGET_LINK_DIRECTORIES(${LIBRARY_NAME} PUBLIC ${MARIADB_CONNECTORC_LIB})
    TARGET_LINK_DIRECTORIES(${STATIC_LIBRARY_NAME} PUBLIC ${MARIADB_CONNECTORC_LIB})
    MESSAGE(STATUS "Found MariaDB Connector/C libraries - ${MARIADB_CONNECTORC_LIB}")
  ELSE()
    MESSAGE(WARNING "Did not find MariaDB Connector/C libraries")
  ENDIF()
  
ENDIF()

INCLUDE(symlink)
CREATE_SYMLINK(lib${LIBRARY_NAME}${CMAKE_STATIC_LIBRARY_SUFFIX} ${STATIC_LIBRARY_NAME} ${INSTALL_LIBDIR})

ADD_DEPENDENCIES(${LIBRARY_NAME} DEPENDENCIES_FOR_PACKAGE)

########## Packaging ##########

# MSI
IF(WIN32)
  IF(WITH_MSI)
    ADD_CUSTOM_COMMAND(TARGET ${LIBRARY_NAME} POST_BUILD
      COMMAND ${CMAKE_COMMAND} ARGS -DDRIVER_LIB_DIR=$<TARGET_FILE_DIR:${LIBRARY_NAME}>
                                    -DPLUGINS_LIB_DIR=$<TARGET_FILE_DIR:dialog>
                                    -DPLUGINS_SUBDIR_NAME=${MARIADB_DEFAULT_PLUGINS_SUBDIR}
                                    -DFILE_IN=${CMAKE_CURRENT_SOURCE_DIR}/wininstall/binaries_dir.xml.in
                                    -DFILE_OUT=${CMAKE_CURRENT_SOURCE_DIR}/wininstall/binaries_dir.xml
                                    -P ${CMAKE_CURRENT_SOURCE_DIR}/cmake/ConfigureFile.cmake
                       )
    ADD_SUBDIRECTORY(wininstall)
  ENDIF()
ELSE()
  IF(APPLE)
    #MESSAGE(STATUS "Configuring to generate PKG package")
    #ADD_SUBDIRECTORY(osxinstall)
  ENDIF()
  INSTALL(TARGETS
          ${LIBRARY_NAME}
          LIBRARY DESTINATION ${INSTALL_LIBDIR}
          COMPONENT SharedLibraries)
  INSTALL(TARGETS
          ${STATIC_LIBRARY_NAME}
          ARCHIVE DESTINATION ${INSTALL_LIBDIR}
          COMPONENT Development)

  MESSAGE(STATUS "Documentation installed to ${INSTALL_DOCDIR}")
  MESSAGE(STATUS "License file installed to ${INSTALL_LICENSEDIR}")
  MESSAGE(STATUS "Public API header files installed to ${INSTALL_INCLUDEDIR}")
  INSTALL(FILES
          ${CMAKE_CURRENT_SOURCE_DIR}/README
          DESTINATION
          ${INSTALL_DOCDIR}
          COMPONENT Documentation)
  INSTALL(FILES
          ${CMAKE_CURRENT_SOURCE_DIR}/COPYING
          DESTINATION
          ${INSTALL_LICENSEDIR}
          COMPONENT Documentation)
  ADD_SUBDIRECTORY(include)
ENDIF()
# Tests. Checking if we have them. May be not the case if we are building from source package
IF(WITH_UNIT_TESTS AND EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/test/CMakeLists.txt")
  ADD_SUBDIRECTORY(test)
  ADD_CUSTOM_COMMAND(TARGET ${LIBRARY_NAME} POST_BUILD
	  COMMAND ${CMAKE_COMMAND} ARGS -E copy $<TARGET_FILE:${LIBRARY_NAME}> test)
  IF(NOT MINGW AND NOT USE_SYSTEM_INSTALLED_LIB)
    ADD_CUSTOM_COMMAND(TARGET ${LIBRARY_NAME} POST_BUILD
	  COMMAND ${CMAKE_COMMAND} ARGS -E copy $<TARGET_FILE:libmariadb> test)
  ENDIF()
ENDIF()

# Packaging
INCLUDE(packaging)
MESSAGE(STATUS "License File: ${CPACK_RESOURCE_FILE_LICENSE}")
MESSAGE(STATUS "ReadMe File: ${CPACK_PACKAGE_DESCRIPTION_FILE}")
MESSAGE(STATUS "Source Package Filename: ${CPACK_SOURCE_PACKAGE_FILE_NAME}.${CPACK_SOURCE_GENERATOR}")

# install_test/CMakeLists.txt configuration should go after packaging.cmake but before include(CPack). Since CPack included in packaging cmake,
# that configuration has been moved there. For the same reason there also moved the message with package name
