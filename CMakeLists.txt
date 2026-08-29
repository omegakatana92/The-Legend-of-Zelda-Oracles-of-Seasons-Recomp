cmake_minimum_required(VERSION 3.16)
project(Legend_of_Zelda__The___Oracle_of_seasons__USA_ C CXX)

include(CheckIPOSupported)

# Set C standard
set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(GBRECOMP_ENABLE_PERFORMANCE_COUNTERS "Compile generated/runtime performance counters" OFF)

set(GBRECOMP_GENERATED_COMPILE_JOBS "8" CACHE STRING "Maximum concurrent generated-source compiles under Ninja; 0 disables the pool")

# Migrate stale generated-project cache entries when the default profile changes.
set(GBRECOMP_PROFILE_CACHE_VERSION "2")
set(GBRECOMP_APPLIED_PROFILE_CACHE_VERSION "0" CACHE STRING "Internal generated-project profile version")
if(NOT GBRECOMP_APPLIED_PROFILE_CACHE_VERSION STREQUAL GBRECOMP_PROFILE_CACHE_VERSION)
    if(NOT DEFINED GBRECOMP_ENABLE_IPO)
        set(GBRECOMP_ENABLE_IPO OFF CACHE BOOL "Enable interprocedural optimization/LTO for non-Debug builds" FORCE)
    endif()
    if(NOT DEFINED GBRECOMP_ENABLE_STRIP)
        set(GBRECOMP_ENABLE_STRIP ON CACHE BOOL "Strip symbols from generated executables in non-Debug builds" FORCE)
    endif()
    if(NOT DEFINED GBRECOMP_GENERATED_OPT_LEVEL)
        set(GBRECOMP_GENERATED_OPT_LEVEL "1" CACHE STRING "Optimization level for the generated ROM source files" FORCE)
    endif()
    set(GBRECOMP_APPLIED_PROFILE_CACHE_VERSION "${GBRECOMP_PROFILE_CACHE_VERSION}" CACHE STRING "Internal generated-project profile version" FORCE)
endif()

# Default to a smaller, iteration-friendly profile for generated projects.
# Benchmark and release builds can still opt into Release/-O3/IPO explicitly.
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    set(CMAKE_BUILD_TYPE MinSizeRel CACHE STRING "Build type" FORCE)
endif()
if(CMAKE_C_COMPILER_ID MATCHES "GNU|Clang")
    if(CMAKE_BUILD_TYPE STREQUAL "Debug")
        add_compile_options(-O0 -g)
    elseif(CMAKE_BUILD_TYPE STREQUAL "RelWithDebInfo")
        add_compile_options(-O2 -g)
    elseif(CMAKE_BUILD_TYPE STREQUAL "MinSizeRel")
        add_compile_options(-Os)
    else()
        add_compile_options(-O3)
    endif()
    add_compile_options(-ffunction-sections -fdata-sections)
endif()

# Self-contained runtime snapshot embedded by gbrecomp
set(GBRT_DIR "${CMAKE_CURRENT_SOURCE_DIR}/runtime")

# Manual SDL2 setup to avoid mingw64 header conflicts with retcomm toolchain
set(SDL2_INCLUDE_DIR "C:/msys64/mingw64/include/SDL2")
set(SDL2_LIBRARY "C:/msys64/mingw64/lib/libSDL2.dll.a")
set(SDL2MAIN_LIBRARY "C:/msys64/mingw64/lib/libSDL2main.a")
set(SDL2_FOUND TRUE)

# Create runtime library with PPU and platform support
add_library(gbrt STATIC
    ${GBRT_DIR}/src/gbrt.c
    ${GBRT_DIR}/src/gbrt_data_mod.c
    ${GBRT_DIR}/src/gbrt_hash.c
    ${GBRT_DIR}/src/gbrt_host_configuration.c
    ${GBRT_DIR}/src/gbrt_port.c
    ${GBRT_DIR}/src/gbrt_presentation.c
    ${GBRT_DIR}/src/gbrt_semantic.c
    ${GBRT_DIR}/src/differential.c
    ${GBRT_DIR}/src/ppu.c
    ${GBRT_DIR}/src/audio.c
    ${GBRT_DIR}/src/audio_stats.c
    ${GBRT_DIR}/src/interpreter.c
    ${GBRT_DIR}/src/platform_sdl.cpp
)

# Vendored Dear ImGui subset
target_sources(gbrt PRIVATE
    ${GBRT_DIR}/vendor/imgui/imgui.cpp
    ${GBRT_DIR}/vendor/imgui/imgui_draw.cpp
    ${GBRT_DIR}/vendor/imgui/imgui_tables.cpp
    ${GBRT_DIR}/vendor/imgui/imgui_widgets.cpp
    ${GBRT_DIR}/vendor/imgui/backends/imgui_impl_sdl2.cpp
    ${GBRT_DIR}/vendor/imgui/backends/imgui_impl_sdlrenderer2.cpp
)
target_include_directories(gbrt PUBLIC 
    ${GBRT_DIR}/include
    ${GBRT_DIR}/vendor/imgui
    ${SDL2_INCLUDE_DIR}
)
target_link_libraries(gbrt PUBLIC ${SDL2_LIBRARY})
target_compile_definitions(gbrt PUBLIC GB_HAS_SDL2)

if(GBRECOMP_ENABLE_PERFORMANCE_COUNTERS)
    target_compile_definitions(gbrt PUBLIC GBRT_ENABLE_PERFORMANCE_COUNTERS)
endif()

# Main executable
add_executable(Legend_of_Zelda__The___Oracle_of_seasons__USA_
    Legend_of_Zelda__The___Oracle_of_seasons__USA__main.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA_.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_0.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_1.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_2.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_3.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_4.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_5.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_6.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_7.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_8.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_9.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_10.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_11.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_12.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_13.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_14.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_15.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_16.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_17.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_18.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_19.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_20.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_21.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_22.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_23.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_24.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_25.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_26.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_27.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_28.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_29.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_30.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_31.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_32.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_33.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_34.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_35.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_36.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_37.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_38.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_39.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_40.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_41.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_42.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_43.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_44.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_45.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_46.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_47.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_48.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_49.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_50.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_51.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_52.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_53.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_54.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_55.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_56.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_57.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_58.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_59.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_60.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_61.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_62.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_63.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_64.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_65.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_0.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_1.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_2.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_3.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_4.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_5.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_6.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_7.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_8.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_9.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_10.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_11.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_12.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_13.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_14.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_15.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_16.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_17.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_18.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_19.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_20.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_21.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_22.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_23.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_24.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_25.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_26.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_27.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_28.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_29.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_30.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_31.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_32.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_33.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_34.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_35.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_36.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_37.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_38.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_39.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_40.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_41.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_42.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_43.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_44.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_45.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_46.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_47.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_48.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_49.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_50.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_51.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_52.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_53.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_54.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_55.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_56.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_57.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_58.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_59.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_60.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_61.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_62.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_63.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_64.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_65.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_66.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_67.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_68.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_69.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_70.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_71.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_72.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_73.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_74.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_75.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_76.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_77.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_78.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_79.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_80.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_81.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_82.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_83.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_84.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_85.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_86.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_87.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_88.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_89.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_90.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_91.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_92.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_93.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_94.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_95.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_96.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_97.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_98.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_99.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_100.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_101.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_102.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_103.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_104.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_105.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_106.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_107.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_108.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_109.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_110.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_111.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_112.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_113.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_114.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_115.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_116.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_117.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_118.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_119.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_120.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_121.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_122.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_123.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_124.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_125.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_126.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_127.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_128.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_129.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_130.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_131.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_132.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_133.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_134.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_135.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_136.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_137.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_138.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_139.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_140.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_141.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_142.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_143.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_144.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_145.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_146.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_147.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_148.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_149.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_150.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_151.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_152.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_153.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_154.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_155.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_156.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_157.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_158.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_159.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_160.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_161.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_162.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_163.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_164.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_165.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_166.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_167.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_168.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_169.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_170.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_171.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_172.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_173.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_174.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_175.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_176.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_177.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_178.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_179.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_180.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_181.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_182.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_183.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_184.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_185.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_186.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_187.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_188.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_189.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_190.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_191.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_192.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_193.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_194.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_195.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__rom.c
)

# Large generated translation units are memory-intensive. Keep runtime/UI
# compilation parallel while bounding only the generated executable target.
if(CMAKE_GENERATOR MATCHES "Ninja" AND GBRECOMP_GENERATED_COMPILE_JOBS MATCHES "^[1-9][0-9]*$")
    set_property(GLOBAL APPEND PROPERTY JOB_POOLS "gbrecomp_generated_compile_pool=${GBRECOMP_GENERATED_COMPILE_JOBS}")
    set_property(TARGET Legend_of_Zelda__The___Oracle_of_seasons__USA_ PROPERTY JOB_POOL_COMPILE gbrecomp_generated_compile_pool)
endif()

# Generated ROM code defaults to a smaller manual-testing profile.
# Raise the optimization level or enable IPO/LTO explicitly for benchmark/release builds.
set(GBRECOMP_GENERATED_OPT_LEVEL "1" CACHE STRING "Optimization level for the generated ROM source files")
set_property(CACHE GBRECOMP_GENERATED_OPT_LEVEL PROPERTY STRINGS 0 1 2 3)
option(GBRECOMP_ENABLE_IPO "Enable interprocedural optimization/LTO for non-Debug builds" OFF)
option(GBRECOMP_ENABLE_STRIP "Strip symbols from generated executables in non-Debug builds" ON)
set(GBRECOMP_GENERATED_SOURCES
    Legend_of_Zelda__The___Oracle_of_seasons__USA_.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_0.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_1.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_2.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_3.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_4.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_5.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_6.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_7.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_8.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_9.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_10.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_11.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_12.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_13.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_14.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_15.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_16.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_17.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_18.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_19.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_20.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_21.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_22.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_23.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_24.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_25.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_26.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_27.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_28.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_29.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_30.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_31.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_32.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_33.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_34.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_35.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_36.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_37.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_38.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_39.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_40.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_41.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_42.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_43.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_44.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_45.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_46.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_47.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_48.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_49.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_50.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_51.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_52.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_53.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_54.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_55.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_56.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_57.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_58.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_59.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_60.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_61.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_62.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_63.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_64.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__dispatch_chunk_65.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_0.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_1.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_2.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_3.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_4.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_5.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_6.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_7.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_8.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_9.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_10.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_11.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_12.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_13.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_14.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_15.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_16.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_17.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_18.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_19.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_20.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_21.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_22.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_23.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_24.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_25.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_26.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_27.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_28.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_29.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_30.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_31.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_32.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_33.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_34.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_35.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_36.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_37.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_38.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_39.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_40.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_41.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_42.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_43.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_44.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_45.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_46.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_47.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_48.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_49.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_50.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_51.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_52.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_53.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_54.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_55.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_56.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_57.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_58.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_59.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_60.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_61.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_62.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_63.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_64.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_65.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_66.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_67.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_68.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_69.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_70.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_71.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_72.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_73.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_74.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_75.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_76.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_77.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_78.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_79.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_80.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_81.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_82.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_83.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_84.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_85.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_86.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_87.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_88.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_89.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_90.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_91.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_92.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_93.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_94.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_95.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_96.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_97.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_98.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_99.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_100.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_101.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_102.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_103.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_104.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_105.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_106.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_107.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_108.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_109.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_110.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_111.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_112.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_113.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_114.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_115.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_116.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_117.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_118.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_119.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_120.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_121.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_122.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_123.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_124.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_125.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_126.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_127.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_128.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_129.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_130.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_131.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_132.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_133.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_134.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_135.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_136.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_137.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_138.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_139.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_140.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_141.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_142.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_143.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_144.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_145.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_146.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_147.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_148.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_149.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_150.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_151.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_152.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_153.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_154.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_155.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_156.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_157.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_158.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_159.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_160.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_161.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_162.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_163.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_164.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_165.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_166.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_167.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_168.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_169.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_170.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_171.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_172.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_173.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_174.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_175.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_176.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_177.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_178.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_179.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_180.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_181.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_182.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_183.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_184.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_185.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_186.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_187.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_188.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_189.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_190.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_191.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_192.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_193.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_194.c
    Legend_of_Zelda__The___Oracle_of_seasons__USA__funcs_195.c
)
if(NOT CMAKE_BUILD_TYPE STREQUAL "Debug")
    if(MSVC)
        if(GBRECOMP_GENERATED_OPT_LEVEL STREQUAL "0")
            set(GBRECOMP_GENERATED_OPT_FLAG "/Od")
        elseif(GBRECOMP_GENERATED_OPT_LEVEL STREQUAL "1")
            set(GBRECOMP_GENERATED_OPT_FLAG "/O1")
        else()
            set(GBRECOMP_GENERATED_OPT_FLAG "/O2")
        endif()
        set_source_files_properties(${GBRECOMP_GENERATED_SOURCES} PROPERTIES COMPILE_OPTIONS "${GBRECOMP_GENERATED_OPT_FLAG}")
    else()
        set_source_files_properties(${GBRECOMP_GENERATED_SOURCES} PROPERTIES COMPILE_OPTIONS "-O${GBRECOMP_GENERATED_OPT_LEVEL}")
    endif()
endif()

if(NOT MSVC AND NOT CMAKE_BUILD_TYPE STREQUAL "Debug")
    if(APPLE)
        target_link_options(gbrt PRIVATE -Wl,-dead_strip)
        target_link_options(Legend_of_Zelda__The___Oracle_of_seasons__USA_ PRIVATE -Wl,-dead_strip)
    else()
        target_link_options(gbrt PRIVATE -Wl,--gc-sections)
        target_link_options(Legend_of_Zelda__The___Oracle_of_seasons__USA_ PRIVATE -Wl,--gc-sections)
    endif()
endif()

if(GBRECOMP_ENABLE_IPO AND NOT CMAKE_BUILD_TYPE STREQUAL "Debug")
    check_ipo_supported(RESULT GBRECOMP_IPO_SUPPORTED OUTPUT GBRECOMP_IPO_ERROR)
    if(GBRECOMP_IPO_SUPPORTED)
        set_property(TARGET gbrt PROPERTY INTERPROCEDURAL_OPTIMIZATION TRUE)
        set_property(TARGET Legend_of_Zelda__The___Oracle_of_seasons__USA_ PROPERTY INTERPROCEDURAL_OPTIMIZATION TRUE)
    else()
        message(STATUS "GB Recompiled: IPO/LTO not enabled (${GBRECOMP_IPO_ERROR})")
    endif()
endif()

target_link_libraries(Legend_of_Zelda__The___Oracle_of_seasons__USA_ gbrt ${SDL2MAIN_LIBRARY})
if(GBRECOMP_ENABLE_STRIP AND NOT MSVC AND NOT CMAKE_BUILD_TYPE STREQUAL "Debug")
    if(APPLE)
        add_custom_command(TARGET Legend_of_Zelda__The___Oracle_of_seasons__USA_ POST_BUILD
            COMMAND ${CMAKE_STRIP} -Sx $<TARGET_FILE:Legend_of_Zelda__The___Oracle_of_seasons__USA_>
            VERBATIM)
    elseif(CMAKE_STRIP)
        add_custom_command(TARGET Legend_of_Zelda__The___Oracle_of_seasons__USA_ POST_BUILD
            COMMAND ${CMAKE_STRIP} --strip-debug $<TARGET_FILE:Legend_of_Zelda__The___Oracle_of_seasons__USA_>
            VERBATIM)
    endif()
endif()
