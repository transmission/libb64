add_library(libb64_obj OBJECT
    cdecode.c
    cencode.c)
target_include_directories(libb64_obj
    PUBLIC
        $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)

add_library(libb64 ${LIBB64_LIBRARY_TYPE})
target_link_libraries(libb64 PUBLIC libb64_obj)

if(NOT LIBB64_STANDALONE_BUILD)
    add_library(${PROJECT_NAME}::libb64 ALIAS libb64)
endif()

set_target_properties(libb64
    PROPERTIES
        OUTPUT_NAME b64
        VERSION ${CMAKE_PROJECT_VERSION}
        SOVERSION 0d)

if(LIBB64_SHARED AND LIBB64_BOTH)
    add_library(libb64_static STATIC)
    target_link_libraries(libb64_static PUBLIC libb64_obj)
    set_target_properties(libb64_static
        PROPERTIES
            OUTPUT_NAME b64)
endif()

if(LIBB64_ENABLE_INSTALL)
    install(
        TARGETS libb64 libb64_obj
        EXPORT ${PROJECT_NAME}-targets
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR})

    if(LIBB64_BOTH)
        install(
            TARGETS libb64_static
            ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR})
    endif()

    install(
        DIRECTORY ${PROJECT_SOURCE_DIR}/include/
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
endif()
