#
# Copyright 2011-2013 Free Software Foundation, Inc.
#
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.
#

########################################################################
# Setup profiler
########################################################################
if(MSVC)
    include_directories(${CMAKE_SOURCE_DIR}/cmake/msvc)
endif(MSVC)

include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_BINARY_DIR}
    ${CMAKE_SOURCE_DIR}/include
    ${CMAKE_BINARY_DIR}/include
    ${CMAKE_SOURCE_DIR}/lib
    ${CMAKE_BINARY_DIR}/lib
    ${Boost_INCLUDE_DIRS}
)

if( ANDROID )
set(CMAKE_CXX_FLAGS "-fPIE ${CMAKE_CXX_FLAGS}")
    set(LINK_LLOG_ANDROID "-fPIE -pie -Wl,-Bdynamic -L{ANDROID_SYSROOT}/usr/lib -llog")
else()
    set(LINK_LLOG_ANDROID "")
endif()

# MAKE volk_profile
add_executable(volk_profile
    ${CMAKE_CURRENT_SOURCE_DIR}/volk_profile.cc
    ${CMAKE_SOURCE_DIR}/lib/qa_utils.cc
)

if(ENABLE_STATIC_LIBS)
    target_link_libraries(volk_profile volk_static ${Boost_LIBRARIES})
    set_target_properties(volk_profile PROPERTIES LINK_FLAGS "${LINK_LLOG_ANDROID} -static")
else()
    target_link_libraries(volk_profile volk ${Boost_LIBRARIES})
endif()

install(
    TARGETS volk_profile
    DESTINATION bin
    COMPONENT "volk"
)

# MAKE volk-config-info
add_executable(volk-config-info volk-config-info.cc)

if(ENABLE_STATIC_LIBS)
    target_link_libraries(volk-config-info volk_static ${Boost_LIBRARIES})
    set_target_properties(volk-config-info PROPERTIES LINK_FLAGS "${LINK_LLOG_ANDROID} -static")
else()
    target_link_libraries(volk-config-info volk ${Boost_LIBRARIES})
endif()

install(
    TARGETS volk-config-info
    DESTINATION bin
    COMPONENT "volk"
)
