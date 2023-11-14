cmake_minimum_required(VERSION 3.10)
project(ngx_hello_module)

# Path to the Nginx source code
set(NGINX_SOURCE_PATH ${CMAKE_SOURCE_DIR}/nginx)

# Nginx module source files
set(MODULE_SOURCES
        src/ngx_http_hello_world_module.c
)

set(WITH_DEBUG "-O0 -g")

include(ExternalProject)

ExternalProject_Add(
        nginx
        SOURCE_DIR ${NGINX_SOURCE_PATH}
        CONFIGURE_COMMAND cd ${NGINX_SOURCE_PATH} && ./configure --with-debug  --with-cc-opt=${WITH_DEBUG} --add-dynamic-module=${NGINX_SOURCE_PATH}/../src --with-compat
        BUILD_COMMAND make -C ${NGINX_SOURCE_PATH}/ modules
        INSTALL_COMMAND ""
)
ExternalProject_Add(
        nginxall
        SOURCE_DIR ${NGINX_SOURCE_PATH}
        CONFIGURE_COMMAND cd ${NGINX_SOURCE_PATH} && ./configure --with-debug  --with-cc-opt=${WITH_DEBUG}  --add-module=${NGINX_SOURCE_PATH}/../src --with-compat
        BUILD_COMMAND make -C ${NGINX_SOURCE_PATH}
        INSTALL_COMMAND ""
)

#ExternalProject_Add(
#        nginx_single
#        SOURCE_DIR ${NGINX_SOURCE_PATH}
#        CONFIGURE_COMMAND cd ${NGINX_SOURCE_PATH} && ./configure --with-debug  --with-cc-opt=${WITH_DEBUG}   --with-compat
#        BUILD_COMMAND make -C ${NGINX_SOURCE_PATH}
#        INSTALL_COMMAND ""
#)

add_custom_command(
        TARGET nginx
        COMMAND ${CMAKE_COMMAND} -E make_directory ${CMAKE_SOURCE_DIR}/output
        COMMAND ${CMAKE_COMMAND} -E copy ${NGINX_SOURCE_PATH}/objs/*.so ${CMAKE_SOURCE_DIR}/output/
        COMMENT "Copy nginx module to ${CMAKE_SOURCE_DIR}/output/"
)
# Nginx module name
set(MODULE_NAME ngx_hello_module)

# Include directories
include_directories(${NGINX_SOURCE_PATH}/src/core)
include_directories(${NGINX_SOURCE_PATH}/src/event)
include_directories(${NGINX_SOURCE_PATH}/src/os/unix)
include_directories(${NGINX_SOURCE_PATH}/objs)
include_directories(${NGINX_SOURCE_PATH}/src/http)
include_directories(${NGINX_SOURCE_PATH}/src/http/modules)
include_directories(${NGINX_SOURCE_PATH}/src/mail)
include_directories(${NGINX_SOURCE_PATH}/src/stream)
# change to your pcre2 include path
include_directories(/usr/local/Cellar/pcre2/10.42/include)
include_directories(src)

set(SOURCE_FILES
        ${NGINX_SOURCE_PATH}/objs/ngx_modules.c)

add_library(nginxapp  ${MODULE_SOURCES})