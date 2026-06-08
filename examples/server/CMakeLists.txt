set(TARGET sd-server)

add_executable(${TARGET}
    ../common/common.cpp
    ../common/log.cpp
    ../common/media_io.cpp
    main.cpp
    runtime.cpp
    async_jobs.cpp
    routes_index.cpp
    routes_openai.cpp
    routes_sdapi.cpp
    routes_sdcpp.cpp
)
if(APPLE)
    sd_set_macos_rpaths(${TARGET})
endif()

install(TARGETS ${TARGET} RUNTIME)
target_link_libraries(${TARGET} PRIVATE gguf-diffusion ${CMAKE_THREAD_LIBS_INIT})
if(SD_WEBP)
    target_compile_definitions(${TARGET} PRIVATE SD_USE_WEBP)
    target_link_libraries(${TARGET} PRIVATE webp libwebpmux)
endif()
if(SD_WEBM)
    target_compile_definitions(${TARGET} PRIVATE SD_USE_WEBM)
    target_link_libraries(${TARGET} PRIVATE webm)
endif()

# due to httplib; it contains a pragma for MSVC, but other things need explicit flags
if(WIN32 AND NOT MSVC)
    target_link_libraries(${TARGET} PRIVATE ws2_32)
endif()

target_compile_features(${TARGET} PUBLIC c_std_11 cxx_std_17)
