# This file is part of libmicrohttpd.
# (C) 2006-2014 Christian Grothoff (and other contributing authors)
#
# libmicrohttpd is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published
# by the Free Software Foundation; either version 3, or (at your
# option) any later version.
#
# libmicrohttpd is distributed in the hope that it will be useful, but
# WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
# General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with libmicrohttpd; see the file COPYING.  If not, write to the
# Free Software Foundation, Inc., 59 Temple Place - Suite 330,
# Boston, MA 02111-1307, USA.
#
#
# Process this file with autoconf to produce a configure script.
#
#
AC_PREREQ(2.57)
LT_PREREQ([2.4.0])
AC_INIT([libmicrohttpd], [0.9.34],[libmicrohttpd@gnu.org])
AM_INIT_AUTOMAKE([silent-rules] [subdir-objects])
AC_CONFIG_HEADERS([MHD_config.h])
AC_CONFIG_MACRO_DIR([m4])
AH_TOP([#define _GNU_SOURCE  1])

LIB_VERSION_CURRENT=33
LIB_VERSION_REVISION=0
LIB_VERSION_AGE=23
AC_SUBST(LIB_VERSION_CURRENT)
AC_SUBST(LIB_VERSION_REVISION)
AC_SUBST(LIB_VERSION_AGE)

LIBSPDY_VERSION_CURRENT=0
LIBSPDY_VERSION_REVISION=0
LIBSPDY_VERSION_AGE=0
AC_SUBST(LIBSPDY_VERSION_CURRENT)
AC_SUBST(LIBSPDY_VERSION_REVISION)
AC_SUBST(LIBSPDY_VERSION_AGE)


if test `uname -s` = "OS/390"
then
# configure binaries for z/OS
  if test -z "$CC"
  then
    CC=`pwd`"/contrib/xcc"
    chmod +x $CC || true
  fi
  if test -z "$CPP"
  then
    CPP="c89 -E"
  fi
  if test -z "$CXXCPP"
  then
    CXXCPP="c++ -E -+"
  fi
#  _CCC_CCMODE=1
#  _C89_CCMODE=1
fi

# Checks for programs.
AC_PROG_AWK
AC_PROG_INSTALL
AC_PROG_LN_S
AC_PROG_MAKE_SET
AC_CANONICAL_HOST
AM_PROG_CC_C_O
LT_INIT([win32-dll])
LT_LANG([Windows Resource])
AC_C_BIGENDIAN

PACKAGE_VERSION_MAJOR=${PACKAGE_VERSION%.*.*}
PACKAGE_VERSION_MINOR=${PACKAGE_VERSION%.*}; PACKAGE_VERSION_MINOR=${PACKAGE_VERSION_MINOR#*.}
PACKAGE_VERSION_SUBMINOR=${PACKAGE_VERSION#*.*.}
AC_SUBST([PACKAGE_VERSION_MAJOR])
AC_SUBST([PACKAGE_VERSION_MINOR])
AC_SUBST([PACKAGE_VERSION_SUBMINOR])
AC_CONFIG_FILES([src/microhttpd/microhttpd_dll_res.rc])

AC_CHECK_MEMBER([struct sockaddr_in.sin_len],
   [ AC_DEFINE(HAVE_SOCKADDR_IN_SIN_LEN, 1, [Do we have sockaddr_in.sin_len?])
   ],
   [],
   [
      #include <sys/types.h>
      #include <sys/socket.h>
      #include <netinet/in.h>
   ])

AC_CHECK_PROG(HAVE_CURL_BINARY,[curl],true,false)
AM_CONDITIONAL(HAVE_CURL_BINARY,$HAVE_CURL_BINARY)
AC_CHECK_PROG(HAVE_MAKEINFO_BINARY,[makeinfo],true,false)
AM_CONDITIONAL(HAVE_MAKEINFO_BINARY,$HAVE_MAKEINFO_BINARY)

# set GCC options
# use '-fno-strict-aliasing', but only if the compiler can take it
if gcc -fno-strict-aliasing -S -o /dev/null -xc /dev/null >/dev/null 2>&1;
then
 CFLAGS="-fno-strict-aliasing $CFLAGS"
fi

# for pkg-config
MHD_LIBDEPS=""
epoll=0
# Check system type
case "$host_os" in
*darwin* | *rhapsody* | *macosx*)
     AC_DEFINE_UNQUOTED(OSX,1,[This is an OS X system])
     CFLAGS="-no-cpp-precomp -fno-common $CFLAGS"
     AM_CONDITIONAL(HAVE_GNU_LD, false)
     ;;
freebsd*)
     AC_DEFINE_UNQUOTED(SOMEBSD,1,[This is a BSD system])
     AC_DEFINE_UNQUOTED(FREEBSD,1,[This is a FreeBSD system])
     AM_CONDITIONAL(HAVE_GNU_LD, true)
     CFLAGS="-D_THREAD_SAFE $CFLAGS"
     ;;
openbsd*)
     AC_DEFINE_UNQUOTED(SOMEBSD,1,[This is a BSD system])
     AC_DEFINE_UNQUOTED(OPENBSD,1,[This is an OpenBSD system])
     AM_CONDITIONAL(HAVE_GNU_LD, true)
     ;;
netbsd*)
     AC_DEFINE_UNQUOTED(SOMEBSD,1,[This is a BSD system])
     AC_DEFINE_UNQUOTED(NETBSD,1,[This is a NetBSD system])
     AM_CONDITIONAL(HAVE_GNU_LD, true)
     ;;
*solaris*)
     AC_DEFINE_UNQUOTED(SOLARIS,1,[This is a Solaris system])
     AC_DEFINE_UNQUOTED(_REENTRANT,1,[Need with solaris or errno doesnt work])
     AM_CONDITIONAL(HAVE_GNU_LD, false)
     LDFLAGS="$LDFLAGS -lpthread"
     ;;
*arm-linux*)
     AC_DEFINE_UNQUOTED(LINUX,1,[This is a Linux kernel])
     AC_DEFINE_UNQUOTED(HAVE_LISTEN_SHUTDOWN,1,[can use shutdown on listen sockets])
     CFLAGS="-D_REENTRANT -fPIC -pipe $CFLAGS"
     AM_CONDITIONAL(HAVE_GNU_LD, true)
     AC_MSG_CHECKING(whether to support epoll)
     AC_ARG_ENABLE([epoll],
		AS_HELP_STRING([--disable-epoll],
			[disable epoll]),
		[enable_epoll=${enableval}],
		[enable_epoll=yes])
     AC_MSG_RESULT($enable_epoll)
     ;;
*linux*)
     AC_DEFINE_UNQUOTED(LINUX,1,[This is a Linux kernel])
     AC_DEFINE_UNQUOTED(HAVE_LISTEN_SHUTDOWN,1,[can use shutdown on listen sockets])
     AM_CONDITIONAL(HAVE_GNU_LD, true)
     AC_MSG_CHECKING(whether to support epoll)
     AC_ARG_ENABLE([epoll],
		AS_HELP_STRING([--disable-epoll],
			[disable epoll]),
		[enable_epoll=${enableval}],
		[enable_epoll=yes])
     AC_MSG_RESULT($enable_epoll)
     ;;
*cygwin*)
     AC_DEFINE_UNQUOTED(CYGWIN,1,[This is a Cygwin system])
     AM_CONDITIONAL(HAVE_GNU_LD, false)
     LDFLAGS="$LDFLAGS"
     os_is_windows=yes
     ;;
*mingw*)
     AC_DEFINE_UNQUOTED(MINGW,1,[This is a MinGW system])
     AC_DEFINE_UNQUOTED(WINDOWS,1,[This is a Windows system])
     LDFLAGS="$LDFLAGS -lws2_32"
     AM_CONDITIONAL(HAVE_GNU_LD, true)
     AC_CHECK_HEADERS([winsock2.h ws2tcpip.h],, AC_MSG_ERROR([[Winsock2 headers are required for W32]]))
     AC_CACHE_CHECK([for MS lib utility], [ac_cv_use_ms_lib_tool],
       [[mslibcheck=`lib 2>&1`
        if [[ $mslibcheck = "Microsoft (R) Library Manager"* ]]; then
          ac_cv_use_ms_lib_tool=yes
        else
          ac_cv_use_ms_lib_tool=no
        fi
         ]])
     if test "x$ac_cv_use_ms_lib_tool" = "xyes"; then
       AC_SUBST([MS_LIB_TOOL], [[lib]])
     fi
     AC_SUBST([lt_cv_objdir])
     os_is_windows=yes
     os_is_native_w32=yes
     ;;
*openedition*)
     AC_DEFINE_UNQUOTED(OS390,1,[This is a OS/390 system])
     AM_CONDITIONAL(HAVE_GNU_LD, false)
    ;;
*)
     AC_MSG_RESULT(Unrecognised OS $host_os)
     AC_DEFINE_UNQUOTED(OTHEROS,1,[Some strange OS])
#    You might want to find out if your OS supports shutdown on listen sockets,
#    and extend the switch statement; if we do not have 'HAVE_LISTEN_SHUTDOWN',
#    pipes are used instead to signal 'select'.
#    AC_DEFINE_UNQUOTED(HAVE_LISTEN_SHUTDOWN,1,[can use shutdown on listen sockets])
     AM_CONDITIONAL(HAVE_GNU_LD, false)
;;
esac

AM_CONDITIONAL(HAVE_W32, [test "x$os_is_native_w32" = "xyes"])
w32_shared_lib_exp=no
if test "x$enable_shared" = "xyes" && test "x$os_is_native_w32" = "xyes"; then
  if test "x$ac_cv_use_ms_lib_tool" = "xyes" || test -n "$DLLTOOL"; then
    w32_shared_lib_exp=yes
  else
    AC_MSG_WARN([[GNU dlltool or MS lib.exe is required for creating shared library export on W32]])
    AC_MSG_WARN([[Export library libmicrohttpd.lib will not be created]])
  fi
fi
AM_CONDITIONAL(W32_SHARED_LIB_EXP, [test "x$w32_shared_lib_exp" = "xyes"])
AM_CONDITIONAL(USE_MS_LIB_TOOL, [test "x$ac_cv_use_ms_lib_tool" = "xyes"])
AM_CONDITIONAL(W32_STATIC_LIB, [test "x$os_is_native_w32" = "xyes"  && test "x$enable_static" = "xyes"])


if test "$enable_epoll" = "yes"
then
 AC_CHECK_HEADERS([sys/epoll.h],
                   [enable_epoll="yes"],
                   [enable_epoll="no"])
fi

if test "$enable_epoll" = "yes"
then
 AC_DEFINE([EPOLL_SUPPORT],[1],[include epoll support])
else
 AC_DEFINE([EPOLL_SUPPORT],[0],[disable epoll support])
fi
AM_CONDITIONAL(ENABLE_EPOLL, [test "x$enable_epoll" != "xno"])


CHECK_PTHREAD
LIBS="$PTHREAD_LIBS $LIBS"
AC_SUBST(PTHREAD_LIBS)
AC_SUBST(PTHREAD_LDFLAGS)
AC_SUBST(PTHREAD_CPPFLAGS)

# Check for headers that are ALWAYS required
AC_CHECK_HEADERS([fcntl.h math.h errno.h limits.h stdio.h locale.h sys/stat.h sys/types.h pthread.h],,AC_MSG_ERROR([Compiling libmicrohttpd requires standard UNIX headers files]))

# Check for optional headers
AC_CHECK_HEADERS([sys/types.h sys/time.h sys/msg.h netdb.h netinet/in.h netinet/tcp.h time.h sys/socket.h sys/mman.h arpa/inet.h sys/select.h poll.h gcrypt.h])

AC_CHECK_HEADERS([search.h], AM_CONDITIONAL(HAVE_TSEARCH, true), AM_CONDITIONAL(HAVE_TSEARCH, false))

# Check for pipe/socketpair signaling
AC_MSG_CHECKING([[whether to disable pipes signaling]])
AC_ARG_ENABLE([[pipes]],
	[AS_HELP_STRING([[--disable-pipes]], [[disable internal singalling by pipes and use socket pair instead]])],
	[], [[enable_pipes=yes]])
AS_IF([[test "x$os_is_windows" = "xyes"]], [disable_pipes=yes
        AC_MSG_RESULT([[yes, forced on W32]])],
	[[test "x$enable_pipes" != "xno"]], [disable_pipes=no
        AC_MSG_RESULT([[$disable_pipes]])],
        [AC_LINK_IFELSE([AC_LANG_PROGRAM([[
				#ifdef HAVE_SYS_TYPES_H
				#include <sys/types.h>
				#endif
				#ifdef HAVE_SYS_SOCKET_H
				#include <sys/socket.h>
				#endif
				]],[[
				  int sv[2];
				  if (socketpair(AF_UNIX, SOCK_STREAM, 0, sv) != 0) return 1

				]])],
            [disable_pipes=yes
              AC_MSG_RESULT([[yes, socketpair in available]])],
            [disable_pipes=no
              AC_MSG_RESULT([[no, socketpair in not available]])]
          )
       ])
if test "x$disable_pipes" = "xyes"; then
	AC_DEFINE([[MHD_DONT_USE_PIPES]], [[1]], [Define to use pair of sockets instead of pipes for signaling])
fi

AC_CHECK_FUNCS_ONCE(memmem)
AC_CHECK_FUNCS_ONCE(accept4)
AC_MSG_CHECKING([[for gmtime_s]])
AC_LINK_IFELSE(
  [AC_LANG_PROGRAM(
    [[ #include <time.h>]], [[struct tm now; time_t t; time (&t); gmtime_s (&now, &t)]])
  ],
  [
    AC_DEFINE([HAVE_GMTIME_S], [1], [Define to 1 if you have `gmtime_s' function (only for W32).])
    AC_MSG_RESULT([[yes]])
  ],
  [AC_MSG_RESULT([[no]])
  ])


AC_COMPILE_IFELSE(
  [AC_LANG_PROGRAM(
                   [
                    #if defined HAVE_SYS_TYPES_H
                    #  include <sys/types.h>
                    #endif
                    #if defined HAVE_SYS_SOCKET_H
                    #  include <sys/socket.h>
                    #elif defined HAVE_WINSOCK2_H
                    #  include <winsock2.h>
                    #endif],
                   [
                    #ifndef SOCK_NONBLOCK
                    # error do not have SOCK_NONBLOCK
                    #endif
                   ])
  ],
  [
    AC_DEFINE([HAVE_SOCK_NONBLOCK], [1], [SOCK_NONBLOCK is defined in a socket header])
  ])


AC_SEARCH_LIBS([clock_gettime], [rt], [
				AC_DEFINE(HAVE_CLOCK_GETTIME, 1, [Have clock_gettime])
])

# IPv6
AC_MSG_CHECKING(for IPv6)
AC_TRY_COMPILE([
#include <stdio.h>
#if HAVE_NETINET_IN_H
#include <netinet/in.h>
#endif
#if HAVE_SYS_SOCKET_H
#include <sys/socket.h>
#endif
#if HAVE_WINSOCK2_H
#include <winsock2.h>
#endif
#if HAVE_WS2TCPIP_H
#include <ws2tcpip.h>
#endif
],[
int af=AF_INET6;
int pf=PF_INET6;
struct sockaddr_in6 sa;
printf("%d %d %p\n", af, pf, &sa);
],[
have_inet6=yes;
AC_DEFINE([HAVE_INET6], [1], [Provides IPv6 headers])
],
have_inet6=no
)
AC_MSG_RESULT($have_inet6)

# TCP_CORK
AC_CHECK_DECLS([TCP_CORK], [], [], [[#include <netinet/tcp.h>]])

# TCP_NOPUSH
AC_CHECK_DECLS([TCP_NOPUSH], [], [], [[#include <netinet/tcp.h>]])

HIDDEN_VISIBILITY_CFLAGS=""
case "$host" in
  *-*-mingw*)
    dnl on mingw32 we do -fvisibility=hidden and __declspec(dllexport)
    AC_DEFINE([_MHD_EXTERN], [__attribute__((visibility("default"))) __declspec(dllexport) extern],
              [defines how to decorate public symbols while building])
    HIDDEN_VISIBILITY_CFLAGS="-fvisibility=hidden"
    ;;
  *)
    dnl on other compilers, check if we can do -fvisibility=hidden
    SAVED_CFLAGS="${CFLAGS}"
    CFLAGS="-fvisibility=hidden"
    AC_MSG_CHECKING([for -fvisibility=hidden compiler flag])
    AC_TRY_COMPILE([], [int main (void) { return 0; }],
                   AC_MSG_RESULT(yes)
                   enable_fvisibility_hidden=yes,
                   AC_MSG_RESULT(no)
                   enable_fvisibility_hidden=no)
    CFLAGS="${SAVED_CFLAGS}"

    AS_IF([test "${enable_fvisibility_hidden}" = "yes"], [
      AC_DEFINE([_MHD_EXTERN], [__attribute__((visibility("default"))) extern],
                [defines how to decorate public symbols while building])
      HIDDEN_VISIBILITY_CFLAGS="-fvisibility=hidden"
    ])
    ;;
esac
AC_SUBST(HIDDEN_VISIBILITY_CFLAGS)

# libcurl (required for testing)
SAVE_LIBS=$LIBS

AC_MSG_CHECKING(whether to use libcurl for testing)
AC_ARG_ENABLE([curl],
  [AS_HELP_STRING([--disable-curl],[disable cURL based testcases])],
  [enable_curl=${enableval}],
  [enable_curl=yes])
AC_MSG_RESULT($enable_curl)
curl=0
if test "$enable_curl" = "yes"
then
  LIBCURL_CHECK_CONFIG(,,curl=1,curl=0)
  AC_CHECK_HEADERS([curl/curl.h],[
# Lib cURL & cURL - OpenSSL versions
   MHD_REQ_CURL_VERSION=7.16.4
   MHD_REQ_CURL_OPENSSL_VERSION=0.9.8
   MHD_REQ_CURL_GNUTLS_VERSION=2.8.6
   MHD_REQ_CURL_NSS_VERSION=3.12.0
   AC_DEFINE_UNQUOTED([MHD_REQ_CURL_VERSION], "$MHD_REQ_CURL_VERSION", [required cURL version to run tests])
   AC_DEFINE_UNQUOTED([MHD_REQ_CURL_OPENSSL_VERSION], "$MHD_REQ_CURL_OPENSSL_VERSION", [required cURL SSL version to run tests])
   AC_DEFINE_UNQUOTED([MHD_REQ_CURL_GNUTLS_VERSION], "$MHD_REQ_CURL_GNUTLS_VERSION", [gnuTLS lib version - used in conjunction with cURL])
   AC_DEFINE_UNQUOTED([MHD_REQ_CURL_NSS_VERSION], "$MHD_REQ_CURL_NSS_VERSION", [NSS lib version - used in conjunction with cURL])
 ],[curl=0])
fi

AC_MSG_CHECKING(for magic_open -lmagic)
AC_CHECK_LIB(magic, magic_open,
  [AC_CHECK_HEADERS([magic.h],
   AM_CONDITIONAL(HAVE_MAGIC, true),
   AM_CONDITIONAL(HAVE_MAGIC, false))],
  AM_CONDITIONAL(HAVE_MAGIC, false))


# optional: libmicrospdy support. Enabled by default
AC_MSG_CHECKING(whether to support libmicrospdy)
AC_ARG_ENABLE([spdy],
		AS_HELP_STRING([--disable-spdy],
			[disable libmicrospdy]),
		[enable_spdy=${enableval}],
		[enable_spdy=yes])
AC_MSG_RESULT($enable_spdy)

if test "$enable_spdy" = "yes"
then
 AC_CHECK_HEADERS([openssl/err.h],
                   AC_CHECK_LIB(ssl, SSL_CTX_set_next_protos_advertised_cb,
                    [AM_CONDITIONAL(HAVE_OPENSSL, true)
                     enable_spdy="yes"],
                    [AM_CONDITIONAL(HAVE_OPENSSL, false)
                    enable_spdy="no"]),
                   [AM_CONDITIONAL(HAVE_OPENSSL, false)
                   enable_spdy="no"])
else
 AM_CONDITIONAL(HAVE_OPENSSL, false)
fi

if test "$enable_spdy" = "yes"
then
 AC_DEFINE([SPDY_SUPPORT],[1],[include libmicrospdy support])
else
 AC_DEFINE([SPDY_SUPPORT],[0],[disable libmicrospdy support])
fi
AM_CONDITIONAL(ENABLE_SPDY, [test "x$enable_spdy" != "xno"])
AC_MSG_CHECKING(whether we have OpenSSL and thus can support libmicrospdy)
AC_MSG_RESULT($enable_spdy)

spdy_OPENSSL
# for pkg-config
SPDY_LIBDEPS=""


AC_CHECK_HEADERS([spdylay/spdylay.h],
                 [AM_CONDITIONAL(HAVE_SPDYLAY, true)
		 have_spdylay="yes"],
                 [AM_CONDITIONAL(HAVE_SPDYLAY, false)
                 have_spdylay="no"])

AC_SUBST(SPDY_LIB_LDFLAGS)
# for pkg-config
AC_SUBST(SPDY_LIBDEPS)

AM_CONDITIONAL(ENABLE_MINITASN1,  [test -n " " ] )
AM_CONDITIONAL(ENABLE_OPENSSL,  [test -n "" ] )
AM_CONDITIONAL(HAVE_LD_OUTPUT_DEF,  [test -n "" ] )
AM_CONDITIONAL(HAVE_LD_VERSION_SCRIPT,  [test -n "" ] )



LIBS=$SAVE_LIBS
AM_CONDITIONAL(HAVE_CURL, test x$curl = x1)

# large file support (> 4 GB)
AC_SYS_LARGEFILE
AC_FUNC_FSEEKO

# optional: have error messages ?
AC_MSG_CHECKING(whether to generate error messages)
AC_ARG_ENABLE([messages],
   [AS_HELP_STRING([--disable-messages],
               [disable MHD error messages])],
   [enable_messages=${enableval}],
   [enable_messages=yes])
AC_MSG_RESULT($enable_messages)
if test "$enable_messages" = "yes"
then
 AC_DEFINE([HAVE_MESSAGES],[1],[Include error messages])
else
 AC_DEFINE([HAVE_MESSAGES],[0],[Disable error messages])
fi


# optional: have postprocessor?
AC_MSG_CHECKING(whether to enable postprocessor)
AC_ARG_ENABLE([postprocessor],
   [AS_HELP_STRING([--disable-postprocessor],
               [disable MHD PostProcessor functionality])],
   [enable_postprocessor=${enableval}],
   [enable_postprocessor=yes])
AC_MSG_RESULT($disable_postprocessor)
AM_CONDITIONAL([HAVE_POSTPROCESSOR],test x$enable_postprocessor != xno)


# optional: have zzuf, socat?
AC_CHECK_PROG([HAVE_ZZUF],[zzuf], 1, 0)
AC_CHECK_PROG([HAVE_SOCAT],[socat], 1, 0)
AM_CONDITIONAL(HAVE_ZZUF, test 0 != $HAVE_ZZUF)
AM_CONDITIONAL(HAVE_SOCAT, test 0 != $HAVE_SOCAT)


# libgcrypt linkage: required for HTTPS support
AM_PATH_LIBGCRYPT(1.2.2, gcrypt=true)

# define the minimal version of libgcrypt required
MHD_GCRYPT_VERSION=1:1.2.2
AC_DEFINE_UNQUOTED([MHD_GCRYPT_VERSION], "$MHD_GCRYPT_VERSION", [gcrypt lib version])


# gnutls
gnutls=0
AC_MSG_CHECKING(for gnutls)
AC_ARG_WITH(gnutls,
   [  --with-gnutls=PFX   base of gnutls installation],
   [AC_MSG_RESULT([$with_gnutls])
    case $with_gnutls in
      no)
        ;;
      yes)
        AC_CHECK_HEADERS([gnutls/gnutls.h],
            AC_CHECK_LIB([gnutls], [gnutls_priority_set],
	     gnutls=true))
        ;;
      *)
        LDFLAGS="-L$with_gnutls/lib $LDFLAGS"
        CPPFLAGS="-I$with_gnutls/include $CPPFLAGS"
        AC_CHECK_HEADERS([gnutls/gnutls.h],
            AC_CHECK_LIB([gnutls], [gnutls_priority_set],
              EXT_LIB_PATH="-L$with_gnutls/lib $EXT_LIB_PATH"
              gnutls=true))
        ;;
    esac
   ],
   [AC_MSG_RESULT([--with-gnutls not specified])
    AC_CHECK_HEADERS([gnutls/gnutls.h],
        AC_CHECK_LIB([gnutls], [gnutls_priority_set],
          gnutls=true))])
AM_CONDITIONAL(HAVE_GNUTLS, test x$gnutls = xtrue)
AC_DEFINE_UNQUOTED([HAVE_GNUTLS], $gnutls, [We have gnutls])



# optional: HTTPS support.  Enabled by default
AC_MSG_CHECKING(whether to support HTTPS)
AC_ARG_ENABLE([https],
   [AS_HELP_STRING([--disable-https],
               [disable HTTPS support])],
   [enable_https=${enableval}],
   [enable_https=yes])
if test "$enable_https" = "yes"
then
 if test "$gcrypt" = "true" -a "$gnutls" = "true"
 then
   AC_DEFINE([HTTPS_SUPPORT],[1],[include HTTPS support])
   MHD_LIBDEPS="$LIBGCRYPT_LIBS"
 else
   AC_DEFINE([HTTPS_SUPPORT],[0],[no libgcrypt or libgnutls])
   enable_https="no (lacking libgcrypt or libgnutls)"
 fi
else
  AC_DEFINE([HTTPS_SUPPORT],[0],[disable HTTPS support])
  enable_https="no (disabled)"
fi
AC_MSG_RESULT($enable_https)

AM_CONDITIONAL(ENABLE_HTTPS, test "$enable_https" = "yes")

# optional: HTTP Basic Auth support. Enabled by default
AC_MSG_CHECKING(whether to support HTTP basic authentication)
AC_ARG_ENABLE([bauth],
		AS_HELP_STRING([--disable-bauth],
			[disable HTTP basic Auth support]),
		[enable_bauth=${enableval}],
		[enable_bauth=yes])
if test "$enable_bauth" = "yes"
then
 AC_DEFINE([BAUTH_SUPPORT],[1],[include basic Auth support])
else
 AC_DEFINE([BAUTH_SUPPORT],[0],[disable basic Auth support])
fi
AC_MSG_RESULT($enable_bauth)
AM_CONDITIONAL(ENABLE_BAUTH, [test "x$enable_bauth" != "xno"])

# optional: HTTP Digest Auth support. Enabled by default
AC_MSG_CHECKING(whether to support HTTP digest authentication)
AC_ARG_ENABLE([dauth],
		AS_HELP_STRING([--disable-dauth],
			[disable HTTP basic and digest Auth support]),
		[enable_dauth=${enableval}],
		[enable_dauth=yes])
if test "$enable_dauth" = "yes"
then
 AC_DEFINE([DAUTH_SUPPORT],[1],[include digest Auth support])
else
 AC_DEFINE([DAUTH_SUPPORT],[0],[disable digest Auth support])
fi
AC_MSG_RESULT($enable_dauth)
AM_CONDITIONAL(ENABLE_DAUTH, [test "x$enable_dauth" != "xno"])



MHD_LIB_LDFLAGS="-export-dynamic -no-undefined"

# gcov compilation
AC_MSG_CHECKING(whether to compile with support for code coverage analysis)
AC_ARG_ENABLE([coverage],
              AS_HELP_STRING([--enable-coverage],
                             [compile the library with code coverage support]),
              [use_gcov=${enableval}],
              [use_gcov=no])
AC_MSG_RESULT($use_gcov)
AM_CONDITIONAL([USE_COVERAGE], [test "x$use_gcov" = "xyes"])


AC_SUBST(MHD_LIB_LDFLAGS)

# for pkg-config
AC_SUBST(MHD_LIBDEPS)

AC_SUBST(CPPFLAGS)
AC_SUBST(LIBS)
AC_SUBST(LDFLAGS)
AC_SUBST(EXT_LIB_PATH)
AC_SUBST(EXT_LIBS)

AC_CONFIG_FILES([
libmicrohttpd.pc
Makefile
contrib/Makefile
doc/Makefile
doc/doxygen/Makefile
doc/examples/Makefile
m4/Makefile
src/Makefile
src/include/Makefile
src/platform/Makefile
src/microhttpd/Makefile
src/microspdy/Makefile
src/spdy2http/Makefile
src/examples/Makefile
src/testcurl/Makefile
src/testcurl/https/Makefile
src/testspdy/Makefile
src/testzzuf/Makefile])
AC_OUTPUT

AM_CONDITIONAL(ENABLE_MINITASN1,  [test -n " " ] )
AM_CONDITIONAL(ENABLE_OPENSSL,  [test -n "" ] )
AM_CONDITIONAL(HAVE_LD_OUTPUT_DEF,  [test -n "" ] )
AM_CONDITIONAL(HAVE_LD_VERSION_SCRIPT,  [test -n "" ] )

# Finally: summary
if test "$curl" != 1 ; then
 MSG_CURL="no, many unit tests will not run"
else
 MSG_CURL="yes"
fi

if test "$gcrypt" != true
then
 MSG_GCRYPT="no, HTTPS will not be built"
else
 MSG_GCRYPT="yes"
fi

AC_MSG_NOTICE([Configuration Summary:
  Operating System:  ${host_os}
  libgcrypt:         ${MSG_GCRYPT}
  libcurl (testing): ${MSG_CURL}
  Target directory:  ${prefix}
  Messages:          ${enable_messages}
  Basic auth.:       ${enable_bauth}
  Digest auth.:      ${enable_dauth}
  Postproc:          ${enable_postprocessor}
  HTTPS support:     ${enable_https}
  epoll support:     ${enable_epoll}
  libmicrospdy:      ${enable_spdy}
  spdylay (testing): ${have_spdylay}
])

if test "x$enable_https" = "xyes"
then
 AC_MSG_NOTICE([HTTPS subsystem configuration:
  License         :  LGPL only
 ])
else
 AC_MSG_NOTICE([
  License         :  LGPL or eCos
])
fi
