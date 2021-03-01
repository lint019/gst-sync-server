cmake_minimum_required(VERSION 2.8.12)
set(project_name gst_sync)
project(project_name)

find_package(PkgConfig REQUIRED)
pkg_search_module(GLIB REQUIRED glib-2.0)

pkg_check_modules(GSTREAMER REQUIRED gstreamer-1.0 gstreamer-net-1.0)

pkg_check_modules(JSON-GLIB REQUIRED json-glib-1.0)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Werror -Wno-deprecated -Wno-deprecated-declarations -std=c++11 -pthread")

include_directories(
   ${GSTREAMER_INCLUDE_DIRS}
  ./gst-libs)

include_directories(
  ${GLIB_INCLUDE_DIRS}
  ${JSON-GLIB_INCLUDE_DIRS}
)

link_directories(
  ${GLIB_LIBRARY_DIRS}
  ${GSTREAMER_LIBRARY_DIRS}
  ${JSON-GLIB_LIBRARY_DIRS}
)

set(cpp_source ./gst-libs/gst/sync-server/sync-control-server.c 
               ./gst-libs/gst/sync-server/sync-client.c
               ./gst-libs/gst/sync-server/sync-server.c
               ./gst-libs/gst/sync-server/sync-control-tcp-client.c
               ./gst-libs/gst/sync-server/sync-control-client.c
               ./gst-libs/gst/sync-server/sync-server-info.c
               ./gst-libs/gst/sync-server/sync-control-tcp-server.c)


add_library(gst-libs SHARED  ${cpp_source})

add_executable(test-server ./examples/test-server.c)
add_executable(test-client ./examples/test-client.c)

target_link_libraries(test-client gst-libs ${JSON-GLIB_LIBRARIES} ${GSTREAMER_LIBRARIES} ${GLIB_LIBRARIES} )
target_link_libraries(test-server gst-libs ${JSON-GLIB_LIBRARIES} ${GSTREAMER_LIBRARIES} ${GLIB_LIBRARIES})

install(TARGETS gst-libs LIBRARY  DESTINATION lib)
install(TARGETS test-server test-client RUNTIME DESTINATION bin)

