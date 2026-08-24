add_library(fcitx5-mozc MODULE
    ${MOZC_SOURCE_DIR}/unix/fcitx5/surrounding_text_util.cc
    ${MOZC_SOURCE_DIR}/unix/fcitx5/fcitx_key_event_handler.cc
    ${MOZC_SOURCE_DIR}/unix/fcitx5/fcitx_key_translator.cc
    ${MOZC_SOURCE_DIR}/unix/fcitx5/mozc_client_pool.cc
    ${MOZC_SOURCE_DIR}/unix/fcitx5/mozc_engine_factory.cc
    ${MOZC_SOURCE_DIR}/unix/fcitx5/mozc_engine.cc
    ${MOZC_SOURCE_DIR}/unix/fcitx5/mozc_response_parser.cc
    ${MOZC_SOURCE_DIR}/unix/fcitx5/mozc_state.cc
)

set_target_properties(fcitx5-mozc PROPERTIES PREFIX "")

target_link_libraries(fcitx5-mozc
    Fcitx5::Core
    PkgConfig::Fcitx5Module
    mozc::protocol::protocol
    mozc::base::vlog
    mozc::base::process
    mozc::base::util
    mozc::base::run_level
    mozc::base::init_mozc)

if (ENABLE_SERVER)
    target_sources(fcitx5-mozc PRIVATE ${MOZC_SOURCE_DIR}/unix/fcitx5/mozc_ipc_client.cc)
    target_link_libraries(fcitx5-mozc mozc::client::client)
else()
    target_sources(fcitx5-mozc PRIVATE ${MOZC_SOURCE_DIR}/unix/fcitx5/mozc_direct_client.cc)
    target_link_libraries(fcitx5-mozc
        mozc::engine::engine
        mozc::session::key_info_util
        mozc::session::session_handler
        mozc::data_manager::oss::oss_data_manager)
endif()

install(TARGETS fcitx5-mozc DESTINATION "${CMAKE_INSTALL_LIBDIR}/fcitx5")

install(FILES ${MOZC_SOURCE_DIR}/unix/fcitx5/mozc-addon.conf
    RENAME mozc.conf
    DESTINATION "${FCITX_INSTALL_PKGDATADIR}/addon")
install(FILES ${MOZC_SOURCE_DIR}/unix/fcitx5/mozc.conf
    RENAME mozc.conf
    DESTINATION "${FCITX_INSTALL_PKGDATADIR}/inputmethod")

foreach(mode IN ITEMS direct hiragana katakana_full alpha_half alpha_full katakana_half)
    install(FILES ${MOZC_SOURCE_DIR}/data/images/unix/48x48/${mode}.png
        RENAME fcitx_mozc_${mode}.png
        DESTINATION "${CMAKE_INSTALL_DATADIR}/icons/hicolor/48x48/apps")
endforeach()
install(FILES ${MOZC_SOURCE_DIR}/data/images/product_icon_32bpp-128.png
    RENAME fcitx_mozc.png
    DESTINATION "${CMAKE_INSTALL_DATADIR}/icons/hicolor/128x128/apps")

# TODO: translations and .desktop/metainfo
