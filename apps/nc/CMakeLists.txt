if(BUILD_NC)

add_executable(nc
	atomicio.c
	netcat.c
	socks.c
	compat/socket.c
)

check_function_exists(b64_ntop HAVE_B64_NTOP)
if(HAVE_B64_NTOP)
	target_compile_definitions(nc PRIVATE HAVE_B64_NTOP)
else()
	target_sources(nc PRIVATE compat/base64.c)
endif()

check_function_exists(accept4 HAVE_ACCEPT4)
if(HAVE_ACCEPT4)
	target_compile_definitions(nc PRIVATE HAVE_ACCEPT4)
else()
	target_sources(nc PRIVATE compat/accept4.c)
endif()

check_function_exists(readpassphrase HAVE_READPASSPHRASE)
if(HAVE_READPASSPHRASE)
	target_compile_definitions(nc PRIVATE HAVE_READPASSPHRASE)
else()
	target_sources(nc PRIVATE compat/readpassphrase.c)
endif()

check_function_exists(strtonum HAVE_STRTONUM)
if(HAVE_STRTONUM)
	target_compile_definitions(nc PRIVATE HAVE_STRTONUM)
else()
	target_sources(nc PRIVATE compat/strtonum.c)
endif()

if(NOT "${OPENSSLDIR}" STREQUAL "")
	target_compile_definitions(nc PRIVATE DEFAULT_CA_FILE=\"${OPENSSLDIR}/cert.pem\")
else()
	target_compile_definitions(nc PRIVATE DEFAULT_CA_FILE=\"${CMAKE_INSTALL_PREFIX}/etc/ssl/cert.pem\")
endif()

target_include_directories(nc
		PRIVATE
			.
			./compat)
target_link_libraries(nc tls ${OPENSSL_LIBS})

if(ENABLE_NC)
	if(ENABLE_LIBRESSL_INSTALL)
		install(TARGETS nc DESTINATION ${CMAKE_INSTALL_BINDIR})
		install(FILES nc.1 DESTINATION ${CMAKE_INSTALL_MANDIR}/man1)
	endif(ENABLE_LIBRESSL_INSTALL)
endif()

endif()
