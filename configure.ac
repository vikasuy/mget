#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.
CFLAGS=$CFLAGS
LDFLAGS=$LDFLAGS

AC_PREREQ([2.67])
AC_INIT([mget], [0.1.5], [mget-bugs@googlegroups.com], [mget], [https://github.com/rockdaboot/mget])
AM_INIT_AUTOMAKE

CFLAGS=$CFLAGS
AC_USE_SYSTEM_EXTENSIONS

LT_PREREQ([2.2])
LT_INIT([dlopen])

# Keep version info in configure.ac
# see also "Updating library version information" at
#   http://www.gnu.org/software/libtool/manual/html_node/Updating-version-info.html
AC_SUBST([LIBMGET_SO_VERSION], [0:0:0])
AC_SUBST([LIBMGET_API_VERSION], [0.1])

AC_CONFIG_SRCDIR([src/mget.c])
AC_CONFIG_HEADERS([config.h])
AC_CONFIG_MACRO_DIR([m4])

# Non-verbose make
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])

AC_CANONICAL_HOST
#case $host_os in
#  linux*)
#        ;;
#  *)
#        AC_MSG_ERROR([$host_os is not currently supported])
#        ;;
#esac

# Checks for programs.

# If CFLAGS isn't defined and using gcc, set CFLAGS to something reasonable.
# Otherwise, just prevent autoconf from molesting CFLAGS.
AC_PROG_CC

if test "x$CFLAGS" = "x" ; then
  if test "x$GCC" = "xyes" ; then
   CFLAGS="-g -std=gnu99 -pedantic -fPIC\
 -Wall -Wstrict-prototypes -Wold-style-definition -Wmissing-prototypes\
 -Wwrite-strings -Wformat=2 -Wformat -Wformat-security\
 -Wsign-compare -Wextra -D_FORTIFY_SOURCE=2\
 -Wcast-align -O2"
  fi
else
  CFLAGS="$CFLAGS -fPIC"
fi
if test "x$LDFLAGS" = "x" ; then
  if test "x$GCC" = "xyes" ; then
#    LDFLAGS="-fPIE -pie -Wl,-z,relro,--as-needed"
    LDFLAGS="-fPIE -pie -Wl,--as-needed -ffunction-sections"
  fi
fi
# Append EXTRA_CFLAGS to CFLAGS, if defined.
if test "x$EXTRA_CFLAGS" != "x" ; then
  CFLAGS="$CFLAGS $EXTRA_CFLAGS"
fi

# Check for large file support.  This check needs to come fairly
# early because it could (in principle) affect whether functions and
# headers are available, whether they work, etc.
AC_SYS_LARGEFILE
AC_CHECK_SIZEOF(off_t)

#AM_NLS
#IT_PROG_INTLTOOL([0.40.0])
AC_PROG_LEX
AC_PROG_INSTALL
AC_PROG_LN_S
AM_PROG_CC_C_O

#
# Require C99 support
#
AC_PROG_CC_C99
if test "$ac_cv_prog_cc_c99" = "no" ; then
   AC_ERROR([Compiler does not support C99. Aborting.])
fi

#
# Gettext
#
AM_GNU_GETTEXT([external],[need-ngettext])
AM_GNU_GETTEXT_VERSION([0.18.1])

#
# check for gtk-doc
#
m4_ifdef([GTK_DOC_CHECK], [
GTK_DOC_CHECK([1.15],[--flavour no-tmpl])
],[
AM_CONDITIONAL([ENABLE_GTK_DOC], false)
])

#
# enable creation of man pages
#
AC_ARG_ENABLE(man,[AC_HELP_STRING([--enable-man],
                                  [generate man pages [default=auto]])],enable_man=yes,enable_man=no)

AS_IF([test "$enable_man" != no], [
  AC_PATH_PROG([XSLTPROC], [xsltproc])
  AS_IF([test -z "$XSLTPROC"], [
    AS_IF([test "$enable_man" = yes], [
      AC_MSG_ERROR([xsltproc is required for --enable-man])
    ])
    enable_man=no
  ])
])

#AC_PATH_PROG([XSLTPROC], [xsltproc])
AM_CONDITIONAL(ENABLE_MAN, test x$enable_man != xno)
AC_MSG_CHECKING([whether to generate man pages])
AS_IF([ test "$enable_man" != no ], [
  AC_MSG_RESULT([yes])
], [
  AC_MSG_RESULT([no])
])


# Check for valgrind
ac_enable_valgrind=no
AC_ARG_ENABLE(valgrind-tests,
  AS_HELP_STRING([--enable-valgrind-tests], [enable using Valgrind for tests]),
  [ac_enable_valgrind=$enableval], [ac_enable_valgrind=no])

if test "${ac_enable_valgrind}" = "yes" ; then
  AC_CHECK_PROG(HAVE_VALGRIND, valgrind, yes, no)
  if test "$HAVE_VALGRIND" = "yes" ; then
    VALGRIND_ENVIRONMENT="valgrind --error-exitcode=1 --leak-check=yes --show-reachable=yes --track-origins=yes"
    AC_SUBST(VALGRIND_ENVIRONMENT)
    TESTS_INFO="Test suite will be run under Valgrind"
  else
    TESTS_INFO="Valgrind not found"
  fi
else
  TESTS_INFO="Valgrind testing not enabled"
fi

#AC_ARG_WITH(gnutls, AS_HELP_STRING([--with-gnutls[=PATH]], [compile with SSL support]), , with_gnutls="no")
#if test $with_gnutls != "no"
#then
#  if test $with_gnutls != "yes"
#  then
#    export PKG_CONFIG_PATH="$with_gnutls:$with_gnutls/pkgconfig:$with_gnutls/lib/pkgconfig:$PKG_CONFIG_PATH"
#    GNUTLS_BIN_DIR="$with_gnutls/bin/"
#    AC_SUBST(GNUTLS_BIN_DIR)
#  fi
#  echo "PKG_CONFIG_PATH=$PKG_CONFIG_PATH"
#  PKG_CHECK_MODULES([GNUTLS], [gnutls >= 3.0.11], [AC_DEFINE([WITH_SSL], [1], [Use GNUTLS])])
#  echo "GNUTLS_CFLAGS=$GNUTLS_CFLAGS"
#  echo "GNUTLS_LIBS=$GNUTLS_LIBS"
#  echo "GNUTLS_BIN_DIR=$GNUTLS_BIN_DIR"
#fi

AC_ARG_WITH(gnutls, AS_HELP_STRING([--without-gnutls], [disable GNUTLS SSL support]), with_gnutls=$withval, with_gnutls=yes)
if test $with_gnutls != "no"
then
  AC_CHECK_LIB(gnutls, gnutls_global_init, [with_gnutls=yes; AC_SUBST(GNUTLS_LIBS, "-lgnutls") AC_DEFINE([WITH_GNUTLS], [1], [Use gnutls])], [with_gnutls=no; AC_MSG_WARN(*** GNUTLS was not found. You will not be able to use HTTPS. Fallback to libnettle for Metalink checksumming.)])
  AC_CHECK_LIB(gnutls, gnutls_hash, [with_gnutls_hash=yes])
fi
AM_CONDITIONAL([WITH_GNUTLS], [test $with_gnutls = "yes"])

#AC_DEFINE([WITH_SSL], [1], [Use SSL])]
#AM_CONDITIONAL([WITH_SSL], [test $with_gnutls = "yes"])


if test $with_gnutls != "yes" || test x$with_gnutls_hash != "xyes"
then
  AC_CHECK_LIB(nettle, nettle_md5_init, [with_libnettle=yes; AC_SUBST(NETTLE_LIBS, "-lnettle") AC_DEFINE([WITH_LIBNETTLE], [1], [Use libnettle])], [with_libnettle=no; AC_MSG_WARN(*** LIBNETTLE was not found. Metalink checksumming is not available.)])
fi
AM_CONDITIONAL([WITH_LIBNETTLE], [test x$with_libnettle = "xyes"])


AC_ARG_WITH(zlib, AS_HELP_STRING([--without-zlib], [disable gzip compression support]), with_zlib=$withval, with_zlib=yes)
if test $with_zlib != "no"
then
  AC_CHECK_LIB(z, deflate, [with_zlib=yes; AC_SUBST(ZLIB_LIBS, "-lz") AC_DEFINE([WITH_ZLIB], [1], [Use zlib])], [with_zlib=no; AC_MSG_WARN(*** ZLIB was not found. You will not be able to use gzip (de)compression)])
fi
AM_CONDITIONAL([WITH_ZLIB], [test $with_zlib = "yes"])

AC_ARG_WITH(lzma, AS_HELP_STRING([--without-lzma], [disable LZMA compression support]), with_lzma=$withval, with_lzma=yes)
if test $with_lzma != "no"
then
  AC_CHECK_LIB(lzma, lzma_code, [with_lzma=yes; AC_SUBST(LZMA_LIBS, "-llzma") AC_DEFINE([WITH_LZMA], [1], [Use liblzma])], [with_lzma=no; AC_MSG_WARN(*** liblzma was not found. You will not be able to use LZMA (de)compression)])
fi
AM_CONDITIONAL([WITH_LZMA], [test $with_lzma = "yes"])

AC_ARG_WITH(bz2, AS_HELP_STRING([--without-bzip2], [disable bzip2 compression support]), with_bzip2=$withval, with_bzip2=yes)
if test $with_bzip2 != "no"
then
  AC_CHECK_LIB(bz2, BZ2_bzDecompress, [with_bzip2=yes; AC_SUBST(BZIP2_LIBS, "-lbz2") AC_DEFINE([WITH_BZIP2], [1], [Use libbz2])], [with_bzip2=no; AC_MSG_WARN(*** libbz2 was not found. You will not be able to use bzip2 (de)compression)])
fi
AM_CONDITIONAL([WITH_BZIP2], [test $with_bzip2 = "yes"])

AC_ARG_WITH(libidn2, AS_HELP_STRING([--without-libidn2], [disable IDN2 support]), with_libidn2=$withval, with_libidn2=yes)
if test $with_libidn2 != "no"
then
  AC_CHECK_LIB(idn2, idn2_lookup_u8, [with_libidn2=yes; AC_SUBST(IDN_LIBS, "-lidn2") AC_DEFINE([WITH_LIBIDN2], [1], [Use libidn2])], [with_libidn2=no; AC_MSG_WARN(*** LIBIDN2 was not found. You will not be able to use IDN2008 support)])
  if test x$with_libidn2 = "xyes"
  then
    AC_CHECK_LIB(unistring, u8_tolower, [with_libunistring=yes; AC_SUBST(IDN_LIBS, "-lidn2 -lunistring") AC_DEFINE([WITH_LIBUNISTRING], [1], [Use libunistring])], [with_libunistring=no; AC_MSG_WARN(*** LIBUNISTRING was not found. You will not be able to use IDN2008 on uppercase domains)])
  fi
fi
AM_CONDITIONAL([WITH_LIBUNISTRING], [test x$with_libunistring = "xyes"])
AM_CONDITIONAL([WITH_LIBIDN2], [test $with_libidn2 = "yes"])

if test x$with_libidn2 != "xyes"
then
  AC_ARG_WITH(libidn, AS_HELP_STRING([--without-libidn], [disable IDN support]), with_libidn=$withval, with_libidn=yes)
  if test x$with_libidn != "xno"
  then
    AC_CHECK_LIB(idn, idna_to_ascii_8z, [with_libidn=yes; AC_SUBST(IDN_LIBS, "-lidn") AC_DEFINE([WITH_LIBIDN], [1], [Use libidn])], [with_libidn=no; AC_MSG_WARN(*** LIBIDN was not found. You will not be able to use IDN support)])
  fi
fi
AM_CONDITIONAL([WITH_LIBIDN], [test x$with_libidn = "xyes"])

# Checks for libraries.
AC_CHECK_LIB([pthread], [pthread_attr_init])
AC_CHECK_LIB([rt], [clock_gettime])

AC_SUBST(LIBS, "$GNUTLS_LIBS $ZLIB_LIBS $LZMA_LIBS $BZIP2_LIBS $IDN_LIBS $NETTLE_LIBS $LIBS")

# Checks for header files.
AC_CHECK_HEADERS([\
 fcntl.h inttypes.h libintl.h locale.h netdb.h netinet/in.h stddef.h stdlib.h string.h\
 strings.h sys/socket.h sys/time.h unistd.h crypt.h poll.h idna.h idn2.h stringprep.h\
 langinfo.h unicase.h])

# Checks for typedefs, structures, and compiler characteristics.
#AC_CHECK_HEADER_STDBOOL
AC_C_INLINE
AC_TYPE_INT16_T
AC_TYPE_INT32_T
AC_TYPE_INT8_T
AC_TYPE_OFF_T
AC_TYPE_PID_T
AC_C_RESTRICT
AC_TYPE_SIZE_T
AC_TYPE_SSIZE_T
AC_TYPE_UINT16_T
AC_TYPE_UINT32_T
AC_TYPE_UINT8_T
AC_CHECK_TYPES([ptrdiff_t])

# Checks for library functions.
AC_FUNC_ALLOCA
AC_FUNC_FORK
AC_FUNC_MALLOC
AC_FUNC_MMAP
AC_FUNC_REALLOC
AC_CHECK_FUNCS([\
 clock_gettime dprintf dup2 futimens gettimeofday localtime_r memchr\
 memmove memset mkdir munmap select setlocale socket strcasecmp\
 strchr strdup strerror strncasecmp strndup strrchr strstr strlcpy \
 vasprintf nanosleep usleep iconv])

AC_CONFIG_FILES([Makefile
                 include/Makefile
                 libmget/Makefile
                 src/Makefile
                 po/Makefile.in
                 docs/libmget/Makefile
                 data/Makefile
                 examples/Makefile
                 tests/Makefile
                 libmget-${LIBMGET_API_VERSION}.pc:libmget.pc.in])
AC_OUTPUT

AC_MSG_NOTICE([Summary of build options:

  Version:           ${PACKAGE_VERSION}
  Host OS:           ${host_os}
  Install prefix:    ${prefix}
  Compiler:          ${CC}
  CFlags:            ${CFLAGS} ${CPPFLAGS}
  LDFlags:           ${LDFLAGS}
  Libs:              ${LIBS}
  SSL support:       $with_gnutls
  GZIP compression:  $with_zlib
  BZIP2 compression: $with_bzip2
  LZMA compression:  $with_lzma
  IDN support:       $IDN_LIBS
  Tests:             ${TESTS_INFO}
])
