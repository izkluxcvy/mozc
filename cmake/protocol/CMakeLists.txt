mozc_library(protocol PROTO SOURCES
    commands.proto
    candidate_window.proto
    config.proto
    engine_builder.proto
    user_dictionary_storage.proto
    state.proto
    renderer_command.proto
    renderer_style.proto
)

mozc_library(segmenter_data.proto)
