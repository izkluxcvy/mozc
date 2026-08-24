# Lightweight IPC client
mozc_library(renderer_client.cc DEPENDS
    mozc::base::clock
    mozc::base::process
    mozc::base::system_util
    mozc::base::version
    mozc::base::vlog
    mozc::ipc::ipc
    mozc::ipc::named_event
    mozc::protocol::protocol
)
