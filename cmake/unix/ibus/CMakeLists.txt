mozc_library(ibus_config.proto)

mozc_python_gen_file(build_tools/embed_file.py
    OUTPUTS ibus_config_textproto.inc
    INPUTS unix/ibus/ibus_config.textproto
    ARGS --input=unix/ibus/ibus_config.textproto
         --name=kIbusConfigTextProto
         --as_string_view
         "--skip_until=# proto-file:"
         --output=${CMAKE_CURRENT_BINARY_DIR}/ibus_config_textproto.inc
)
add_custom_target(ibus-config_textproto_inc
    DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/ibus_config_textproto.inc
)
set_target_properties(ibus-config_textproto_inc PROPERTIES EXCLUDE_FROM_ALL TRUE)

set(IBUS_MOZC_SERVER_DIR "${CMAKE_INSTALL_FULL_LIBDIR}/mozc")
set(IBUS_MOZC_PATH "${CMAKE_INSTALL_FULL_LIBDIR}/ibus-mozc/ibus-engine-mozc")
set(IBUS_MOZC_INSTALL_DIR "${CMAKE_INSTALL_FULL_DATADIR}/ibus-mozc")
set(IBUS_MOZC_ICON_PATH "${IBUS_MOZC_INSTALL_DIR}/product_icon.png")

add_custom_command(
    OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/mozc.xml
    DEPENDS ${MOZC_SOURCE_DIR}/unix/ibus/gen_mozc_xml.py
    WORKING_DIRECTORY ${MOZC_SOURCE_DIR}
    COMMAND ${CMAKE_COMMAND} -E env PYTHONPATH=. $<TARGET_FILE:Python3::Interpreter>
            ${MOZC_SOURCE_DIR}/unix/ibus/gen_mozc_xml.py
            --branding=Mozc
            --server_dir=${IBUS_MOZC_SERVER_DIR}
            --ibus_mozc_path=${IBUS_MOZC_PATH}
            --ibus_mozc_icon_path=${IBUS_MOZC_ICON_PATH}
            > ${CMAKE_CURRENT_BINARY_DIR}/mozc.xml
    VERBATIM
)
add_custom_command(
    OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/main.h
    DEPENDS ${MOZC_SOURCE_DIR}/unix/ibus/gen_mozc_xml.py
    WORKING_DIRECTORY ${MOZC_SOURCE_DIR}
    COMMAND ${CMAKE_COMMAND} -E env PYTHONPATH=. $<TARGET_FILE:Python3::Interpreter>
            ${MOZC_SOURCE_DIR}/unix/ibus/gen_mozc_xml.py
            --output_cpp
            --branding=Mozc
            --server_dir=${IBUS_MOZC_SERVER_DIR}
            --ibus_mozc_path=${IBUS_MOZC_PATH}
            --ibus_mozc_icon_path=${IBUS_MOZC_ICON_PATH}
            > ${CMAKE_CURRENT_BINARY_DIR}/main.h
    VERBATIM
)
add_custom_target(ibus-mozc_xml DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/mozc.xml)
add_custom_target(ibus-main_h DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/main.h)
set_target_properties(ibus-mozc_xml ibus-main_h PROPERTIES EXCLUDE_FROM_ALL TRUE)

mozc_library(ibus_config.cc DEPENDS
    mozc::unix::ibus::ibus_config_pb
    mozc::base::file_util
    mozc::base::protobuf_util
    mozc::base::system_util
    mozc::base::text_normalizer
    protobuf::libprotobuf
    absl::log
    absl::status
    absl::statusor
    absl::strings
)
target_include_directories(unix-ibus-ibus_config PRIVATE ${PROJECT_BINARY_DIR})
add_dependencies(unix-ibus-ibus_config ibus-config_textproto_inc ibus-main_h)

mozc_library(ibus_wrapper.cc DEPENDS PkgConfig::Ibus absl::strings)

mozc_library(property_handler.cc DEPENDS
    PkgConfig::Ibus
    mozc::unix::ibus::ibus_wrapper
    mozc::base::const
    mozc::base::file_util
    mozc::base::system_util
    mozc::client::client
    mozc::protocol::protocol
    absl::log
    absl::check
    absl::strings
)

mozc_library(message_translator.cc
    SOURCES
    path_util.cc
    DEPENDS
    mozc::base::util
    absl::check
    absl::strings
)
target_compile_definitions(unix-ibus-message_translator PRIVATE
    MOZC_IBUS_INSTALL_DIR="${IBUS_MOZC_INSTALL_DIR}"
)

mozc_library(candidate_window_handler.cc DEPENDS
    PkgConfig::Ibus
    mozc::unix::ibus::ibus_wrapper
    mozc::base::coordinates
    mozc::protocol::protocol
    mozc::renderer::renderer_client
    absl::log
    absl::check
    absl::strings
)

mozc_library(mozc_engine.cc
    SOURCES
    engine_registrar.cc
    ibus_candidate_window_handler.cc
    key_event_handler.cc
    key_translator.cc
    preedit_handler.cc
    surrounding_text_util.cc
    DEPENDS
    mozc::unix::ibus::candidate_window_handler
    mozc::unix::ibus::ibus_config
    mozc::unix::ibus::ibus_wrapper
    mozc::unix::ibus::property_handler
    mozc::unix::ibus::message_translator
    mozc::base::clock
    mozc::base::const
    mozc::base::file_util
    mozc::base::system_util
    mozc::base::util
    mozc::base::vlog
    mozc::client::client
    mozc::protocol::protocol
    mozc::renderer::renderer_client
    absl::flags
    absl::log
    absl::check
    absl::strings
    absl::str_format
    absl::time
)

add_executable(ibus_mozc
    ${MOZC_SOURCE_DIR}/unix/ibus/main.cc
    ${CMAKE_CURRENT_BINARY_DIR}/main.h
)
target_include_directories(ibus_mozc PRIVATE ${PROJECT_BINARY_DIR})
add_dependencies(ibus_mozc ibus-main_h ibus-mozc_xml)
target_link_libraries(ibus_mozc
    mozc::unix::ibus::ibus_config
    mozc::unix::ibus::ibus_wrapper
    mozc::unix::ibus::mozc_engine
    mozc::unix::ibus::message_translator
    mozc::base::init_mozc
    mozc::base::version
    PkgConfig::Ibus
    absl::flags
    absl::check
    absl::strings
)

set_target_properties(ibus_mozc PROPERTIES OUTPUT_NAME ibus-engine-mozc)
install(TARGETS ibus_mozc DESTINATION "${CMAKE_INSTALL_LIBDIR}/ibus-mozc")
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/mozc.xml
    DESTINATION "${CMAKE_INSTALL_DATADIR}/ibus/component")
install(FILES ${MOZC_SOURCE_DIR}/data/images/product_icon_32bpp-128.png
    RENAME product_icon.png
    DESTINATION "${CMAKE_INSTALL_DATADIR}/ibus-mozc")

foreach(mode IN ITEMS direct hiragana katakana_full alpha_half alpha_full katakana_half)
    install(FILES ${MOZC_SOURCE_DIR}/data/images/unix/48x48/${mode}.png
        DESTINATION "${CMAKE_INSTALL_DATADIR}/ibus-mozc")
endforeach()
foreach(pair IN ITEMS "ui-properties.png:properties.png" "ui-dictionary.png:dictionary.png" "ui-tool.png:tool.png")
    string(REPLACE ":" ";" pair "${pair}")
    list(GET pair 0 src_name)
    list(GET pair 1 dst_name)
    install(FILES ${MOZC_SOURCE_DIR}/data/images/unix/${src_name}
        RENAME ${dst_name}
        DESTINATION "${CMAKE_INSTALL_DATADIR}/ibus-mozc")
endforeach()
