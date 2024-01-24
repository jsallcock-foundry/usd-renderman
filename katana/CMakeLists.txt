set(PLUGIN_NAME usd_renderman_katana)

set(USD_KATANA_LIBRARY_DIR ${KATANA_HOME}/plugins/Resources/Usd/lib)
set(USD_KATANA_INCLUDE_DIR ${KATANA_HOME}/plugins/Resources/Usd/include)

set(RENDERMAN_INCLUDE_DIR $ENV{RMANTREE}/include)
set(RENDERMAN_LIBRARY $ENV{RMANTREE}/lib)

set(USD_LIBRARY_DIR ${KATANA_HOME}/bin)
set(USD_INCLUDE_DIR ${KATANA_HOME}/external/FnUSD/include)

set(PXR_LIB_PREFIX fn)
find_package(USD REQUIRED)

set(GEOLIB_API_SOURCE_DIR ${KATANA_API_SOURCE_DIR})

find_package(Renderman REQUIRED)

set(BOOST_LIBRARY_DIR ${KATANA_HOME}/bin)
set(BOOST_INCLUDE_DIR ${KATANA_HOME}/external/foundryboost/include)
set(BOOST_LIBRARYDIR ${KATANA_HOME}/bin)
set(BOOST_INCLUDEDIR ${KATANA_HOME}/external/foundryboost/include)

set(Boost_NAMESPACE foundryboost)
add_definitions(-DBOOST_ALL_NO_LIB)
# add_definitions(-DBOOST_ALL_DYN_LINK)

find_package(Boost
        COMPONENTS
        filesystem
        serialization
        python39
        system
        thread
        REQUIRED
        )

set(TBB_INCLUDE_DIR ${KATANA_HOME}/external/tbb/include)
set(TBB_LIBRARY ${KATANA_HOME}/bin)
find_package(TBB REQUIRED COMPONENTS tbb)
add_definitions(${TBB_DEFINITIONS})

set(PYTHON_LIBRARY ${KATANA_HOME}/bin/python39.lib)
set(PYTHON_INCLUDE_DIR ${KATANA_HOME}/bin/include/include)
find_package(PythonLibs REQUIRED)

link_directories(
        ${USD_LIBRARY_DIR}
        ${USD_KATANA_LIBRARY_DIR}
        ${KATANA_API_BASE_DIR}/bin
        ${TBB_LIBRARY}
)

include_directories(
        SYSTEM ${KATANA_HOME}/bin/python2.7/include/python2.7
        SYSTEM ${USD_INCLUDE_DIR}
        SYSTEM ${USD_KATANA_INCLUDE_DIR}
        ${TBB_INCLUDE_DIRS}
        SYSTEM ${KATANA_API_INCLUDE_DIR}
        ${CMAKE_SOURCE_DIR}/katana/
        ${Boost_INCLUDE_DIRS}
        SYSTEM ${PYTHON_INCLUDE_DIRS}
)

file(GLOB SRC *.cpp)
add_library(${PLUGIN_NAME} MODULE ${SRC})

# target_compile_options(${PLUGIN_NAME} PRIVATE -Wno-deprecated -Wno-unused-parameter -Wall -Wextra)
target_compile_options(${PLUGIN_NAME} PRIVATE -Wall -DNOMINMAX -DWIN32_LEAN_AND_MEAN -DNOSERVICE -DNOMCX -DNOIME)
target_compile_definitions(${PLUGIN_NAME} PRIVATE TBB_USE_ASSERT TBB_USE_THREADING_TOOLS) 

target_link_libraries(${PLUGIN_NAME}
    #CEL
    ${PYTHON_LIBRARIES}
    ${Boost_LIBRARIES}
    #${Boost_PYTHON_LIBRARY}
    ${TBB_LIBRARIES}
    Katana::FnAttribute
    Katana::FnGeolibOpPlugin
    Katana::FnGeolibServices
    fntf
    fngf
    fnsdf
    fnusd
    fnusdGeom
    fnusdVol
    usdKatana
)
set_target_properties(${PLUGIN_NAME} PROPERTIES PREFIX "")
set_target_properties(${PLUGIN_NAME} PROPERTIES INSTALL_RPATH_USE_LINK_PATH ON)

install(TARGETS ${PLUGIN_NAME} DESTINATION Resources/Ops)
