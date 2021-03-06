## This must come first when building an application that uses Intel
## SGX enclaves. It can build against the Intel SGX SDK or Microsoft's
## OpenEnclave

include $(top_srcdir)/build-aux/sgx_app.am

## It adds a pattern rule for building proxy functions from EDL files:
##
##   %_u.h %_u.c: %.edl
##
## And sets the following automake and Makefile variables:
##
## ----- Intel SGX SDK ----------------------------------------------
##
##   SGXSDK
##   SGXSDK_BINDIR
##   SGXSDK_INCDIR
##   SGXSDK_LIBDIR
##   SGX_URTS_LIB
##   SGX_UAE_SERVICE_LIB
##   SGX_EDGER8R
##
##   AM_CPPFLAGS = -I$(SGXSDK_INCDIR)
##   AM_LDFLAGS = -L$(SGXSDK_LIBDIR)
##
## ----- OpenEnclave ------------------------------------------------
##
##   OE
##   OE_BINDIR
##   OE_INCDIR
##   OE_LIBDIR
##   OE_APP_LIBS
##   OE_EDGER8R
##
##   AM_CPPFLAGS = -I$(OE_INCDIR)
##   AM_LDFLAGS = -L$(OE_LIBDIR)
##
## ----- All --------------------------------------------------------
##
##   SGXSSL
##   SGXSSL_BINDIR
##   SGXSSL_INCDIR
##   SGXSSL_LIBDIR
##


DIST_SUBDIRS = EnclaveHash
if SGX_ENABLED
SUBDIRS = EnclaveHash
else !SGX_ENABLED
SUBDIRS =
endif


## Supply additional flags to edger8r here. 
##
## if SGX_WITH_SGXSDK
## SGX_EDGER8R_FLAGS=
## else
## OE_EDGER8R_FLAGS=
## endif


## Needed to make our pattern rule work

if SGX_ENABLED
EnclaveHash.edl: EnclaveHash/EnclaveHash.edl
	ln -s $?
endif


## Additional automake variables
##
## AM_CPPFLAGS += 
## AM_CFLAGS = 
## AM_CXXFLAGS = 

AM_CPPFLAGS += -fno-builtin-memset

## Additional targets to remove with 'make clean'. You must list
## any edger8r generated files here.

if SGX_ENABLED
CLEANFILES = EnclaveHash_u.c EnclaveHash_u.h EnclaveHash.edl \
	$(SIGNED_ENCLAVE)

## Copy the signed enclave to the current directory

if SGX_WITH_SGXSDK
SIGNED_ENCLAVE=EnclaveHash.signed.so
EnclaveHash.signed.so: EnclaveHash/EnclaveHash.signed.so
else
SIGNED_ENCLAVE=EnclaveHash.so.signed
EnclaveHash.so.signed: EnclaveHash/EnclaveHash.so.signed
endif
	ln -s $?

endif


## The build target

bin_PROGRAMS = storesecret


## You can't use $(wildcard ...) with automake so all source files 
## have to be explicitly listed.

storesecret_SOURCES = main.c
if SGX_WITH_SGXSDK
## Run-time feature detection is not available in OpenEnclave
storesecret_SOURCES += sgx_detect_linux.c sgx_stub.c
endif
if SGX_ENABLED
nodist_storesecret_SOURCES = EnclaveHash_u.c EnclaveHash_u.h
BUILT_SOURCES = EnclaveHash_u.c EnclaveHash_u.h
EXTRA_storesecret_DEPENDENCIES = $(SIGNED_ENCLAVE)
endif


## Library flags. At minimum, an Intel SGX SDK application will need:
##
##   -l$(SGX_URTS_LIB)
##
## Applications that use platform services or perform attestation
## will also need:
##
##  -l$(SGX_UA_SERVICE_LIB)
##
## Use the variables, not the actualy library names to ensure these
## targets work on simulation builds.
##
## OpenEnclave applications need, at minimum, $(OE_APP_LIBS)

if SGX_WITH_SGXSDK
storesecret_LDADD=-l$(SGX_URTS_LIB) -ldl -l:libsgx_capable.a -lpthread
else
storesecret_LDADD=$(OE_APP_LIBS)
endif

