#    Copyright (C) 2012 Modelon AB

#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the BSD style license.

#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    FMILIB_License.txt file for more details.

#    You should have received a copy of the FMILIB_License.txt file
#    along with this program. If not, contact Modelon AB <http://www.modelon.com>.

if(NOT C99SNPRINTFDIR)
    set(C99SNPRINTFDIR ${FMILIBRARYHOME}/ThirdParty/c99_snprintf)
    set(C99SNPRINTF_LIBRARIES c99snprintf)    

INCLUDE (CheckIncludeFiles) 
CHECK_INCLUDE_FILES(stdarg.h HAVE_STDARG_H) 
CHECK_INCLUDE_FILES(stdlib.h HAVE_STDLIB_H)
CHECK_INCLUDE_FILES(stddef.h HAVE_STDDEF_H)
CHECK_INCLUDE_FILES(stdint.h HAVE_STDINT_H)
CHECK_INCLUDE_FILES(stdlib.h HAVE_STDLIB_H)
CHECK_INCLUDE_FILES(inttypes.h 	HAVE_INTTYPES_H)
CHECK_INCLUDE_FILES(locale.h   	HAVE_LOCALE_H)

INCLUDE (CheckSymbolExists)
INCLUDE (CheckFunctionExists)
CHECK_SYMBOL_EXISTS(vsnprintf stdio.h HAVE_VSNPRINTF_SYM)
CHECK_SYMBOL_EXISTS(snprintf  stdio.h HAVE_SNPRINTF_SYM)
CHECK_SYMBOL_EXISTS(vasprintf stdio.h HAVE_VASPRINTF_SYM)
CHECK_SYMBOL_EXISTS(asprintf  stdio.h HAVE_ASPRINTF_SYM)
CHECK_FUNCTION_EXISTS(vsnprintf HAVE_VSNPRINTF_FCN)
CHECK_FUNCTION_EXISTS(snprintf  HAVE_SNPRINTF_FCN)
CHECK_FUNCTION_EXISTS(vasprintf HAVE_VASPRINTF_FCN)
CHECK_FUNCTION_EXISTS(asprintf  HAVE_ASPRINTF_FCN)

MACRO(FMIL_EVAL_CONDITION var_to_store_cond_eval)
   IF(${ARGN})
     SET(${var_to_store_cond_eval} 1)
   ELSE(${ARGN})
     SET(${var_to_store_cond_eval} 0)
   ENDIF(${ARGN})
ENDMACRO(FMIL_EVAL_CONDITION)
FMIL_EVAL_CONDITION(HAVE_VSNPRINTF  HAVE_VSNPRINTF_SYM OR HAVE_VSNPRINTF_FCN)
FMIL_EVAL_CONDITION(HAVE_SNPRINTF   HAVE_SNPRINTF_SYM  OR HAVE_SNPRINTF_FCN)
FMIL_EVAL_CONDITION(HAVE_VASPRINTF  HAVE_VASPRINTF_SYM OR HAVE_VASPRINTF_FCN)
FMIL_EVAL_CONDITION(HAVE_ASPRINTF   HAVE_ASPRINTF_SYM  OR HAVE_ASPRINTF_FCN)

CHECK_FUNCTION_EXISTS(localeconv    	HAVE_LOCALECONV)
 
 INCLUDE (CheckTypeSize) 
 # SET(CMAKE_EXTRA_INCLUDE_FILES header)
#CHECK_TYPE_SIZE(type variable)
# SET(CMAKE_EXTRA_INCLUDE_FILES) 
 CHECK_TYPE_SIZE("long double" HAVE_LONG_DOUBLE)
 CHECK_TYPE_SIZE("long long int" 	HAVE_LONG_LONG_INT)
 CHECK_TYPE_SIZE("unsigned long long int"   	HAVE_UNSIGNED_LONG_LONG_INT)
 
SET(CMAKE_EXTRA_INCLUDE_FILES locale.h)
CHECK_TYPE_SIZE("struct lconv" LCONV)
SET(CMAKE_EXTRA_INCLUDE_FILES)
include(CheckStructHasMember)
CHECK_STRUCT_HAS_MEMBER ("struct lconv" decimal_point locale.h HAVE_LCONV_DECIMAL_POINT)
CHECK_STRUCT_HAS_MEMBER ("struct lconv" thousands_sep locale.h HAVE_LCONV_THOUSANDS_SEP)
INCLUDE (CheckSymbolExists) 
    
CHECK_TYPE_SIZE(intmax_t 	HAVE_INTMAX_T)
CHECK_TYPE_SIZE(uintmax_t   	HAVE_UINTMAX_T)
CHECK_TYPE_SIZE(uintptr_t  	HAVE_UINTPTR_T)
CHECK_TYPE_SIZE(ptrdiff_t    	HAVE_PTRDIFF_T)

include(CheckSymbolExists)
CHECK_SYMBOL_EXISTS(va_copy stdarg.h HAVE_VA_COPY)
CHECK_SYMBOL_EXISTS(__va_copy stdarg.h HAVE___VA_COPY)

set(CHECKS
        HAVE_VSNPRINTF
        HAVE_SNPRINTF
        HAVE_VASPRINTF
        HAVE_ASPRINTF
        HAVE_STDARG_H
        HAVE_STDDEF_H
        HAVE_STDINT_H
        HAVE_STDLIB_H
        HAVE_INTTYPES_H
        HAVE_LOCALE_H
        HAVE_LOCALECONV
        HAVE_LCONV_DECIMAL_POINT
        HAVE_LCONV_THOUSANDS_SEP
        HAVE_LONG_DOUBLE
        HAVE_LONG_LONG_INT
        HAVE_UNSIGNED_LONG_LONG_INT
        HAVE_INTMAX_T
        HAVE_UINTMAX_T
        HAVE_UINTPTR_T
        HAVE_PTRDIFF_T
        HAVE_VA_COPY
        HAVE___VA_COPY)

foreach(check ${CHECKS})
    if(${${check}})
        set(DEFINES ${DEFINES} -D${check}=${${check}})
    endif()
endforeach()

if(NOT HAVE_VSNPRINTF)
    set(DEFINES ${DEFINES} -Dvsnprintf=rpl_vsnprintf)
endif()

if(NOT HAVE_SNPRINTF)
    set(DEFINES ${DEFINES} -Dsnprintf=rpl_snprintf)
endif()

if(NOT HAVE_VASPRINTF)
    set(DEFINES ${DEFINES} -Dvasprintf=rpl_vasprintf)
endif()

if(NOT HAVE_ASPRINTF)
    set(DEFINES ${DEFINES} -Dasprintf=rpl_asprintf)
endif()

add_definitions(${DEFINES})

file(WRITE ${CMAKE_BINARY_DIR}/c99snprintf_defs "${DEFINES}")

add_library(c99snprintf c99-snprintf_1.1/snprintf.c)

if(CYGWIN) 
message("not tested")
endif(CYGWIN)

endif()

