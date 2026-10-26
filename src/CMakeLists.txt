add_library(ncrypto ncrypto.cpp engine.cpp aead.cpp)
if(NCRYPTO_EVP_ONLY)
    target_compile_definitions(ncrypto PUBLIC NCRYPTO_EVP_ONLY=1)
else()
    target_compile_definitions(ncrypto PRIVATE NCRYPTO_ENGINE_COMPAT=1)
endif()

# Enable strict warning flags for ncrypto sources only
if(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang|AppleClang")
    target_compile_options(ncrypto PRIVATE
        -Werror
        -Wextra
        -Wimplicit-fallthrough
    )
    if(NOT NCRYPTO_EVP_ONLY)
        target_compile_options(ncrypto PRIVATE
            -Wno-unused-parameter
            -Wno-deprecated-declarations  # OpenSSL 3.0 deprecates many APIs we intentionally use
        )
    endif()
elseif(MSVC)
    target_compile_options(ncrypto PRIVATE
        /WX     # Treat warnings as errors
        /W3     # Warning level 3 (production quality)
        /wd4267 # Conversion from 'size_t' to smaller type
        /wd4244 # Conversion, possible loss of data
        /wd4305 # Truncation from 'int' to 'bool'
        /wd4127 # Conditional expression is constant
    )
    if(NOT NCRYPTO_EVP_ONLY)
        target_compile_options(ncrypto PRIVATE /wd4100)
    endif()
endif()

if (NCRYPTO_SHARED_LIBS)
    target_link_libraries(ncrypto PUBLIC OpenSSL::SSL OpenSSL::Crypto)
else()
    target_link_libraries(ncrypto PUBLIC ssl crypto)

    if (NCRYPTO_BSSL_LIBDECREPIT_MISSING)
        target_compile_definitions(ncrypto PUBLIC NCRYPTO_BSSL_LIBDECREPIT_MISSING=1)
    else()
        target_link_libraries(ncrypto PUBLIC decrepit)
    endif()
endif()
target_include_directories(ncrypto
    PUBLIC
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}>
)
