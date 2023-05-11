set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_VERSION 1)
cmake_minimum_required(VERSION 3.25)

set(CMAKE_C_COMPILER riscv-none-embed-gcc)
set(CMAKE_CXX_COMPILER riscv-none-embed-g++)
set(CMAKE_ASM_COMPILER riscv-none-embed-gcc)
set(CMAKE_AR riscv-none-embed-ar)
set(CMAKE_OBJCOPY riscv-none-embed-objcopy)
set(CMAKE_OBJDUMP riscv-none-embed-objdump)
set(SIZE riscv-none-embed-size)
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

project(example C CXX ASM)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_C_STANDARD 11)

add_compile_options(-march=rv32imacxw -mabi=ilp32 -msmall-data-limit=8 -msave-restore -fsigned-char)
add_compile_options(-ffunction-sections -fdata-sections -fno-common -fmessage-length=0)
add_compile_options(-Wunused -Wuninitialized)
add_compile_options($<$<COMPILE_LANGUAGE:ASM>:-x$<SEMICOLON>assembler-with-cpp>)

if ("${CMAKE_BUILD_TYPE}" STREQUAL "Release")
    message(STATUS "Maximum optimization for speed")
    add_compile_options(-Ofast)
elseif ("${CMAKE_BUILD_TYPE}" STREQUAL "RelWithDebInfo")
    message(STATUS "Maximum optimization for speed, debug info included")
    add_compile_options(-Ofast -g)
elseif ("${CMAKE_BUILD_TYPE}" STREQUAL "MinSizeRel")
    message(STATUS "Maximum optimization for size")
    add_compile_options(-Os)
else ()
    message(STATUS "Minimal optimization, debug info included")
    add_compile_options(-Og -g)
endif ()

include_directories(Core Debug Peripheral/inc User)

file(GLOB_RECURSE SOURCES "Core/*.*" "Debug/*.*" "Peripheral/src/*.*" "User/*.*" "Startup/startup_ch32v30x_D8C.S")

set(LINKER_SCRIPT ${CMAKE_SOURCE_DIR}/Ld/Link.ld)

add_link_options(-Wl,-gc-sections,--print-memory-usage,-Map=${PROJECT_BINARY_DIR}/${PROJECT_NAME}.map)
add_link_options(-nostartfiles --specs=nano.specs --specs=nosys.specs)
add_link_options(-T ${LINKER_SCRIPT})

# uncommnet to use printf float
#add_link_options(-u _printf_float)

add_executable(${PROJECT_NAME}.elf
        ${SOURCES}
        ${LINKER_SCRIPT})

set(HEX_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.hex)
set(BIN_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.bin)

add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
        COMMAND ${CMAKE_OBJCOPY} -Oihex $<TARGET_FILE:${PROJECT_NAME}.elf> ${HEX_FILE}
        COMMAND ${CMAKE_OBJCOPY} -Obinary $<TARGET_FILE:${PROJECT_NAME}.elf> ${BIN_FILE}
        COMMENT "Building ${HEX_FILE}
Building ${BIN_FILE}")
