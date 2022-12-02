cmake_minimum_required(VERSION 3.23)

project(QuickJS
    LANGUAGES C
    VERSION 2021.3.27
)

option(CONFIG_LTO "使用link time optimization(更小、更快的可执行文件，但构建更慢)" ON)
option(BUILD_EXAMPLES "是否构建examples" ON)
option(BUILD_TESTS "是否构建tests" ON)

# check_ipo_supported(RESULT result OUTPUT output)
# if(result)
#   set_property(TARGET foo PROPERTY INTERPROCEDURAL_OPTIMIZATION TRUE)
# else()
#   message(WARNING "IPO is not supported: ${output}")
# endif()

add_compile_definitions(CONFIG_BIGNUM CONFIG_LTO)
add_compile_definitions(CONFIG_VERSION="${PROJECT_VERSION}")

include_directories(.)

set(QJS_LIB_OBJS
quickjs.c libregexp.c libunicode.c cutils.c quickjs-libc.c
libbf.c 
)

include(CheckIncludeFile)
macro(check_include filename)
    string(REGEX REPLACE "[/\.]" "_" outstr ${filename} )
    string(TOUPPER ${outstr} upoutstr)
    message("outstr: ${outstr} ${upoutstr}")
    check_include_file(${filename} HAVE_${upoutstr})
    message("output: ${HAVE_${upoutstr}}")
    unset(outstr)
    unset(upoutstr)
endmacro()
check_include("unistd.h")
check_include("sys/time.h")
check_include("sys/stat.h")
check_include("dirent.h")
check_include("utime.h")
check_include("inttypes.h")
# message("HAVE_UNISTD_H:${HAVE_UNISTD_H}")
# check_include_file("unistd.h" HAVE_UNISTD_H)
# message("HAVE_UNISTD_H:${HAVE_UNISTD_H}")
# check_include_file("unistd.h" HAVE_UNISTD_H)


add_executable(qjsc     
    qjsc.c ${QJS_LIB_OBJS})

# execute_process(
#     COMMAND ${qjsc} "-c -o repl.c -m repl.js"
#     WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
# )

# execute_process(
#     COMMAND ${qjsc} "-c -fbignum -o qjscalc.c -m qjscalc.js"
#     WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
# )


set(QJS_OBJS
    qjs.c repl.c ${QJS_LIB_OBJS} 
    qjscalc.c
)

add_executable(qjs ${QJS_OBJS})


# add_executable(run-test262  ${QJS_LIB_OBJS})
# add_executable(qjscalc)
# add_library(libquickjs STATIC ${QJS_LIB_OBJS})

# qjsc -c -o repl.c -m repl.js # => 生成repl.c
# qjsc -fbignum -c -o qjscalc.c qjscalc.js # => 生成qjscalc.c
# unicode_gen unicode libunicode-table.h # 生成libunicode-table.h文件

# add_library(libregexp)
# add_library(libunicode)
# add_library(libbf)

# add_executable(examples/hello)
# add_executable(examples/hello_module)
# add_executable(examples/test_fib)
# add_library(examples/fib.so)
# add_library(examples/point.so)

# mkdir -p .obj .obj/examples .obj/tests
