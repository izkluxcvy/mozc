mozc_library(user_history_predictor.proto)
mozc_library(user_history_storage.cc DEPENDS
    mozc::prediction::user_history_predictor_pb
    mozc::base::config_file_stream
    mozc::base::file_util
    mozc::base::hash
    mozc::base::util
    mozc::storage::encrypted_string_storage
)
mozc_library(user_history_predictor.cc DEPENDS
    mozc::prediction::user_history_predictor_pb
    mozc::prediction::user_history_storage
    mozc::base::util
    mozc::base::clock
    mozc::base::vlog
    mozc::base::hash
    mozc::composer::composer
    mozc::converter::segments
    mozc::base::config_file_stream
    mozc::storage::encrypted_string_storage
    mozc::rewriter::variants_rewriter
)
mozc_library(suggestion_filter.cc DEPENDS
    absl::statusor
    absl::log
    mozc::base::hash
    mozc::base::util
    mozc::storage::existence_filter)
mozc_library(single_kanji_decoder.cc DEPENDS
    mozc::dictionary::pos_matcher
    mozc::base::util
    mozc::composer::composer
    mozc::dictionary::single_kanji_dictionary
)
mozc_library(number_decoder.cc DEPENDS
    absl::log
    absl::check
    absl::raw_hash_set
    mozc::base::util
    mozc::dictionary::pos_matcher
    mozc::protocol::protocol
)
mozc_library(zero_query_dict.h)
mozc_library(result_filter.cc DEPENDS
    mozc::prediction::result
    mozc::prediction::suggestion_filter
    mozc::base::util
    mozc::converter::connector
    mozc::protocol::protocol
)
mozc_library(decoder_util.cc DEPENDS
    absl::btree
    absl::span
    mozc::prediction::result
    mozc::base::number_util
    mozc::base::util
    mozc::protocol::protocol
)
mozc_library(dictionary_decoder.cc DEPENDS
    absl::btree
    absl::algorithm
    mozc::prediction::decoder_util
    mozc::prediction::result
    mozc::base::util
    mozc::engine::modules
    mozc::dictionary::dictionary_token
)
mozc_library(english_decoder.cc DEPENDS
    mozc::prediction::decoder_util
    mozc::prediction::result
    mozc::base::util
    mozc::dictionary::dictionary_token
    mozc::engine::modules
    mozc::transliteration::transliteration
)
mozc_library(zero_query_decoder.cc DEPENDS
    mozc::prediction::decoder_util
    mozc::prediction::result
    mozc::prediction::zero_query_dict
    mozc::base::util
    mozc::base::number_util
    mozc::dictionary::dictionary_token
    mozc::engine::modules
    mozc::protocol::protocol
    mozc::transliteration::transliteration
)
mozc_library(handwriting_decoder.cc DEPENDS
    mozc::prediction::decoder_util
    mozc::prediction::realtime_decoder
    mozc::prediction::result
    mozc::base::util
    mozc::dictionary::dictionary_token
    mozc::engine::modules
    mozc::protocol::protocol
)
mozc_library(dictionary_prediction_aggregator.cc DEPENDS
    mozc::protocol::protocol
    mozc::base::util
    mozc::base::number_util
    mozc::converter::segments
    mozc::composer::composer
    mozc::prediction::decoder_util
    mozc::prediction::dictionary_decoder
    mozc::prediction::result
    mozc::prediction::number_decoder
    mozc::prediction::result_filter
    mozc::prediction::english_decoder
    mozc::prediction::zero_query_decoder
    mozc::prediction::handwriting_decoder
    mozc::engine::modules
)
mozc_library(result.cc DEPENDS
    absl::log
)
mozc_library(dictionary_predictor.cc DEPENDS
    mozc::protocol::protocol
    mozc::composer::composer
    mozc::converter::segments
    mozc::converter::connector
    mozc::converter::segmenter
    mozc::prediction::suggestion_filter
    mozc::prediction::result
    mozc::dictionary::single_kanji_dictionary
    mozc::prediction::dictionary_prediction_aggregator
    mozc::prediction::realtime_decoder
    mozc::engine::modules)
mozc_library(realtime_decoder.cc DEPENDS
    mozc::base::util
    mozc::converter::segments
    mozc::converter::converter_util
    mozc::dictionary::dictionary_token
)
mozc_library(predictor.cc DEPENDS
    mozc::protocol::protocol
    mozc::composer::composer
    mozc::converter::segments
    mozc::prediction::user_history_predictor
    mozc::prediction::dictionary_predictor
    mozc::prediction::realtime_decoder
    mozc::engine::modules
)
mozc_executable(gen_suggestion_filter_main.cc DEPENDS
    mozc::base::file_stream
    mozc::base::util
    mozc::base::hash
    mozc::base::multifile
    mozc::base::init_mozc
    mozc::base::codegen_bytearray_stream
    mozc::storage::existence_filter
)
