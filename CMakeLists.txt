if ( NOT LUOS_LIB_PATH )
    message( STATUS "Path to Luos library not set!" )
    return()
endif ()

if ( NOT NODE_ROLE )
    message( STATUS "Node role is undefined!" )
    return()
endif()

set( HAL_SOURCE_PATH    "${LUOS_LIB_PATH}/LuosHAL" )
set( LUOS_SOURCE_PATH   "${LUOS_LIB_PATH}/Luos" )

set( luos_lib_name "luos" )

# Config library
add_library(luos_config INTERFACE)

add_library( ${luos_lib_name}
    # HAL
    "${HAL_SOURCE_PATH}/luos_hal.c"

    "${HAL_SOURCE_PATH}/board/luos_hal_board.c"
    "${HAL_SOURCE_PATH}/flash/luos_hal_flash.c"
    "${HAL_SOURCE_PATH}/systick/luos_hal_systick.c"
    "${HAL_SOURCE_PATH}/timer/luos_hal_timer.c"

    "${HAL_SOURCE_PATH}/ble/${NODE_ROLE}/luos_hal_ble.c"
    "${HAL_SOURCE_PATH}/com/${NODE_ROLE}/luos_hal_com.c"
    "${HAL_SOURCE_PATH}/ptp/${NODE_ROLE}/luos_hal_ptp.c"

    # Luos
    "${LUOS_SOURCE_PATH}/src/luos.c"
    "${LUOS_SOURCE_PATH}/src/luos_utils.c"
    "${LUOS_SOURCE_PATH}/src/routing_table.c"
    "${LUOS_SOURCE_PATH}/src/streaming.c"
    "${LUOS_SOURCE_PATH}/Robus/src/msg_alloc.c"
    "${LUOS_SOURCE_PATH}/Robus/src/port_manager.c"
    "${LUOS_SOURCE_PATH}/Robus/src/reception.c"
    "${LUOS_SOURCE_PATH}/Robus/src/robus.c"
    "${LUOS_SOURCE_PATH}/Robus/src/target.c"
    "${LUOS_SOURCE_PATH}/Robus/src/transmission.c"
)

add_compile_definitions(
    BSP_DEFINES_ONLY
    CONFIG_GPIO_AS_PINRESET
    DEBUG
)

target_compile_options(${luos_lib_name} PUBLIC
    ${${ARCH}_DEFINES})

target_compile_definitions(${luos_lib_name} PUBLIC
    ${USER_DEFINITIONS}
    ${${PLATFORM}_DEFINES}
    ${${SOFTDEVICE}_DEFINES}
    ${${BOARD}_DEFINES})

target_link_libraries( ${luos_lib_name} PUBLIC
    # Common
    nrf5_memobj
    nrf5_balloc
    nrf5_atfifo
    nrf5_atflags
    nrf5_atomic
    nrf5_ringbuf
    nrf5_section
    nrf5_strerror
    # Drivers
    nrf5_nrfx_gpiote
    nrf5_nrfx_systick
    # External
    nrf5_ext_fprintf
    nrf5_ext_segger_rtt
    # Logger
    nrf5_log
    nrf5_log_backend_serial
    nrf5_log_backend_rtt
    nrf5_log_default_backends
    # Flash storage
    nrf5_fstorage
    nrf5_fstorage_sd
    # Application
    nrf5_app_error
    nrf5_app_util_platform
    nrf5_app_timer
    # BSP
    nrf5_boards
    nrf5_bsp_defs
    nrf5_sdh
)

target_include_directories( ${luos_lib_name} PUBLIC
    # HAL
    "${HAL_SOURCE_PATH}"
    "${HAL_SOURCE_PATH}/ble"
    "${HAL_SOURCE_PATH}/ble/common"
    "${HAL_SOURCE_PATH}/ble/${NODE_ROLE}"
    "${HAL_SOURCE_PATH}/board"
    "${HAL_SOURCE_PATH}/systick"
    "${HAL_SOURCE_PATH}/com"
    "${HAL_SOURCE_PATH}/flash"
    "${HAL_SOURCE_PATH}/ptp"
    "${HAL_SOURCE_PATH}/timer"

    # Luos
    "${LUOS_SOURCE_PATH}/inc"
    "${LUOS_SOURCE_PATH}/OD/"
    "${LUOS_SOURCE_PATH}/Robus/inc/"
)
