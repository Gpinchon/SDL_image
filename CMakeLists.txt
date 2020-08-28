CMAKE_MINIMUM_REQUIRED(VERSION 3.0)

PROJECT(SDL_image C)

if(NOT DEFINED SDL2_LIBRARY)
	message(ERROR "SDL2_LIBRARY missing")
endif()

if(NOT DEFINED SDL2_INCLUDE_DIR)
	message(ERROR "SDL2_INCLUDE_DIR missing")
endif()

option(SDL2_IMAGE_STATIC "ENABLE STATIC BUILD" ON)
option(SDL2_IMAGE_SHARED "ENABLE SHARED BUILD" ON)
option(SDL2_IMAGE_BUILD_PNG "Custom PNG Location, else find_package is used" OFF)
option(SDL2_IMAGE_BUILD_JPG "Custom JPG Location, else find_package is used" OFF)
option(SDL2_IMAGE_BMP "Enable parsing for BMP" ON)
option(SDL2_IMAGE_GIF "Enable parsing for GIF" ON)
option(SDL2_IMAGE_JPG "Enable parsing for JPG" ON)
option(SDL2_IMAGE_LBM "Enable parsing for LBM" ON)
option(SDL2_IMAGE_PCX "Enable parsing for PCX" ON)
option(SDL2_IMAGE_PNG "Enable parsing for PNG" ON)
option(SDL2_IMAGE_PNM "Enable parsing for PNM" ON)
option(SDL2_IMAGE_TGA "Enable parsing for TGA" ON)
option(SDL2_IMAGE_TIF "Enable parsing for TIF" OFF)
option(SDL2_IMAGE_WEBP "Enable parsing for WEBP" OFF) # BROKEN
option(SDL2_IMAGE_XCF "Enable parsing for XCF" ON)
option(SDL2_IMAGE_XPM "Enable parsing for XPM" ON)
option(SDL2_IMAGE_XV "Enable parsing for XV" ON)

set(ENABLE_DEFS "")
set(LINK_LIBS ${SDL2_LIBRARY})
set(INCLUDE_DIRS ${SDL2_INCLUDE_DIR})

if(SDL2_IMAGE_BMP)
    set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_BMP)
endif()

if(SDL2_IMAGE_GIF)
    set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_GIF)
endif()

if(SDL2_IMAGE_JPG)
    set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_JPG)
    if(NOT SDL2_IMAGE_BUILD_JPG)
        find_package(JPEG REQUIRED)
    endif(NOT SDL2_IMAGE_BUILD_JPG)
    set(LINK_LIBS "${LINK_LIBS}" "${JPEG_LIBRARIES}")
    set(INCLUDE_DIRS "${INCLUDE_DIRS}" "${JPEG_INCLUDE_DIRS}")
endif()

if(SDL2_IMAGE_LBM)
    set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_LBM)
endif()

if(SDL2_IMAGE_PCX)
    set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_PCX)
endif()

if(SDL2_IMAGE_PNG)
    set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_PNG)
    if (NOT SDL2_IMAGE_BUILD_PNG)
        find_package(PNG REQUIRED)
    endif(NOT SDL2_IMAGE_BUILD_PNG)
    set(LINK_LIBS "${LINK_LIBS}" "${PNG_LIBRARIES}")
    set(INCLUDE_DIRS "${INCLUDE_DIRS}" "${PNG_INCLUDE_DIRS}")
endif()

if(SDL2_IMAGE_PNM)
    set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_PNM)
endif()

if(SDL2_IMAGE_TGA)
    set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_TGA)
endif()

if(SDL2_IMAGE_TIF)
    set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_TIF)
    find_package(TIFF REQUIRED)
    set(LINK_LIBS "${LINK_LIBS}" "${TIFF_LIBRARIES}")
    set(INCLUDE_DIRS "${INCLUDE_DIRS}" "${TIFF_INCLUDE_DIRS}")
endif()

if(SDL2_IMAGE_WEBP)
    set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_WEBP)
    find_package(WebP REQUIRED) # BROKEN, debian doesn't install the cmake config files, so find_package will fail
    set(LINK_LIBS "${LINK_LIBS}" "${WebP_LIBRARIES}")
    set(INCLUDE_DIRS "${INCLUDE_DIRS}" "${WebP_INCLUDE_DIRS}")
endif()

if(SDL2_IMAGE_XCF)
    set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_XCF)
endif()

if(SDL2_IMAGE_XV)
    set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_XV)
endif()

set(SRC_C_FILES
    IMG.c
    IMG_png.c
    IMG_bmp.c
    IMG_gif.c
    IMG_jpg.c
    IMG_lbm.c
    IMG_pcx.c
    IMG_pnm.c
    IMG_svg.c
    IMG_tga.c
    IMG_tif.c
    IMG_webp.c
    IMG_WIC.c
    IMG_xcf.c
    IMG_xpm.c
    IMG_xv.c
    IMG_xxx.c
)

set(SRC_H_FILES  
    SDL_image.h
    miniz.h
)

set(SRC_FILES ${SRC_C_FILES} ${SRC_H_FILES})

if(SDL2_IMAGE_STATIC)
add_library(SDL2_image-static STATIC ${SRC_FILES})
target_link_libraries(SDL2_image-static ${LINK_LIBS})
target_include_directories(SDL2_image-static PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}")
target_compile_definitions(SDL2_image-static PUBLIC "${ENABLE_DEFS}")
endif()
if(SDL2_IMAGE_SHARED)
add_library(SDL2_image SHARED ${SRC_FILES})
target_link_libraries(SDL2_image ${LINK_LIBS})
target_include_directories(SDL2_image-static PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}")
target_compile_definitions(SDL2_image PUBLIC "${ENABLE_DEFS}")
endif()

include_directories(${INCLUDE_DIRS})
