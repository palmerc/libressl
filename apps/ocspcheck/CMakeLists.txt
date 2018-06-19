if(NOT MSVC)

add_executable(ocspcheck
	http.c
	ocspcheck.c
)

check_function_exists(memmem HAVE_MEMMEM)
if(HAVE_MEMMEM)
        target_compile_definitions(ocspcheck PRIVATE HAVE_MEMMEM)
else()
        target_sources(ocspcheck PRIVATE compat/memmem.c)
endif()

if(NOT "${OPENSSLDIR}" STREQUAL "")
	target_compile_definitions(ocspcheck PRIVATE DEFAULT_CA_FILE=\"${OPENSSLDIR}/cert.pem\")
else()
	target_compile_definitions(ocspcheck PRIVATE DEFAULT_CA_FILE=\"${CMAKE_INSTALL_PREFIX}/etc/ssl/cert.pem\")
endif()

target_include_directories(ocspcheck
		PRIVATE
		.
		./compat)
target_link_libraries(ocspcheck tls ${OPENSSL_LIBS})

if(ENABLE_LIBRESSL_INSTALL)
	install(TARGETS ocspcheck DESTINATION ${CMAKE_INSTALL_BINDIR})
	install(FILES ocspcheck.8 DESTINATION ${CMAKE_INSTALL_MANDIR}/man8)

endif(ENABLE_LIBRESSL_INSTALL)

endif()
