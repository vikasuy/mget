# got some hints from https://gitorious.org/openismus-playground/examplelib/source

SUBDIRS = po include libmget src data examples docs/libmget tests
#ACLOCAL_AMFLAGS = -I m4
ACLOCAL_AMFLAGS = ${ACLOCAL_FLAGS}

# Enable GTK-Doc during make distcheck
DISTCHECK_CONFIGURE_FLAGS = --enable-gtk-doc --enable-man

## Install the generated pkg-config file (.pc) into the expected location for
## architecture-dependent package configuration information. Occasionally,
## pkg-config files are also used for architecture-independent data packages,
## in which case the correct install location would be $(datadir)/pkgconfig.
pkgconfigdir = $(libdir)/pkgconfig
pkgconfig_DATA = libmget-$(LIBMGET_API_VERSION).pc
