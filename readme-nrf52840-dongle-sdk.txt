
readme-nrf52840-dongle-sdk.txt
toc:  pca10059 examples
      testing example copied from ble_app_blinky
      ses versions
      novelbits.io nrf52840 usb dongle tutorial 2
      nrf-connect started ble-connectivity 4.1.2
      nrf52840 dongle programming tutorial by nordic
      dongle uart log


pca10059 examples
    ~/nRF5SDK15209412b96/nRF5_SDK_15.2.0_9412b96/examples
    $ find . -name 'pca10059*' -type d
    ./ble_peripheral/ble_app_blinky/pca10059
    ./ble_peripheral/ble_app_hrs/pca10059
    ./connectivity/ble_connectivity/pca10059
    ./dfu/open_bootloader/pca10059_usb
    ./dfu/open_bootloader/pca10059_usb_debug
    ./peripheral/blinky/pca10059
    ./peripheral/cli/pca10059


testing example: copied from ble_app_blinky:

    $ diff -ru ble_app_blinky/ dongle_ble_app_blinky/
    diff -ru ble_app_blinky/pca10059/s140/config/sdk_config.h dongle_ble_app_blinky/pca10059/s140/config/sdk_config.h
    --- ble_app_blinky/pca10059/s140/config/sdk_config.h    2021-03-13 09:03:49.473532300 -0800
    +++ dongle_ble_app_blinky/pca10059/s140/config/sdk_config.h     2021-03-14 10:16:57.795274500 -0700
    @@ -7370,7 +7370,7 @@
    -#define NRF_LOG_BACKEND_RTT_ENABLED 0
    +#define NRF_LOG_BACKEND_RTT_ENABLED 1
    @@ -7403,7 +7403,7 @@
    -#define NRF_LOG_BACKEND_UART_ENABLED 1
    +#define NRF_LOG_BACKEND_UART_ENABLED 0


ses versions
    ses 3.4.0 sdk-15.0.0 not able to debug the dongle. downloading appears to succeed but image is not working.
    ses 5.4.0c sdk-15.2.0 same. 
    use nrf-connect 3.6.1 to program. it works. 
        clicked the nrf-connect installer twice, it installed jlink 6.88a and nordic drivers the second time. 
    work sequence: when connected the dongle swi to dk p19
        [1] build in ses 5.4.0c. 
        [2] turn off dk power switch. 
        [3] download in nrf-connect programmer. twice each time. 
        [4] turn on dk power switch. debug in ses. 


novelbits.io nrf52840 usb dongle tutorial 2
https://www.novelbits.io/nrf52840-usb-dongle-tutorial-2/
    modify from the ble template
        [1] make a copy of the directory.
        [2] rename the "pca10056" dierctory into "pca10059". 
        [3] copy a "flash_placement.xml" file from another pca10059 example directory.
        [5] change the ".emProject" file, change "BOARD_PCA10056" into "BOARD_10059".
        [6] change the "NRF_LOG_BACKEND_UART_ENABLED" to 0 to disable it.


nrf-connect started ble-connectivity 4.1.2
nrf-connect started: pc-nrfconnect-ble, ble-connectivity 4.1.2+jul-14-2020-05-48-48 softdevice api version 5
    com3, baud 1000000. 


nrf52840 dongle programming tutorial by nordic
https://devzone.nordicsemi.com/nordic/short-range-guides/b/getting-started/posts/nrf52840-dongle-programming-tutorial
    MBR, bootloader, DFU-trigger:
        [1] MBR in flash: 0-0xfff. in memory 0x20000000-to-0x20000007 8 bytes to forward interrupts. 
            MBR supports safe updates of the bootloader. itself is never updated. 
        [2] bootloader in flash 0x000e0000. 
        [3] DFU-trigger library. in application. 
    Adapt a simple example: 
        [1] Preprocessor definition: change "BOARD_PCA10056" to "BOARD_PCA10059".
        [2] Linker configuration: Set "FLASH_START=0x1000". "FLASH_SIZE=0xEF000". "RAM_START=0x20000008". 
                                      "RAM_SIZE=0x3FFF8.
    Adapting firmware to set REGOUT0 properly: 
        [1] must be set to 3V in order to be debugged from a DK. this is inside "bsp_board_init()" if the 
            BOARD_PCA10059 is defined. otherwise, use the following code: 

                if ((NRF_UICR->REGOUT0 & UICR_REGOUT0_VOUT_Msk) ==
                    (UICR_REGOUT0_VOUT_DEFAULT << UICR_REGOUT0_VOUT_Pos))
                {
                    NRF_NVMC->CONFIG = NVMC_CONFIG_WEN_Wen;
                    while (NRF_NVMC->READY == NVMC_READY_READY_Busy){}
                
                    NRF_UICR->REGOUT0 = (NRF_UICR->REGOUT0 & ~((uint32_t)UICR_REGOUT0_VOUT_Msk)) |
                                        (UICR_REGOUT0_VOUT_3V0 << UICR_REGOUT0_VOUT_Pos);
                
                    NRF_NVMC->CONFIG = NVMC_CONFIG_WEN_Ren;
                    while (NRF_NVMC->READY == NVMC_READY_READY_Busy){}
                
                    // System reset is needed to update UICR registers.
                    NVIC_SystemReset();
                }
    Debugging using a DK: 
        connect the dongle to the DK P19. the dongle needs its own power supply. 
            or short SB47 to supply power to the dongle, in that the DK won't be able to debug its own target. 
        https://infocenter.nordicsemi.com/index.jsp?topic=%2Fug_nrf52840_dk%2FUG%2Fdk%2Fhw_debug_out.html
        if P19 or P20 connects an external target, the DK debugs the external target not the on-board target. 
        if both P19 and P20 connect to external targets, the DK debugs P19 target. 


experiment: this does not work
    for the above:
        [2] Linker configuration: Set "FLASH_START=0x1000". "FLASH_SIZE=0xEF000". "RAM_START=0x20000008". 
                                      "RAM_SIZE=0x3FFF8.
    change .emProject: 
         30       linker_section_placement_macros="FLASH_PH_START=0x0;FLASH_PH_SIZE=0x100000;
                  RAM_PH_START=0x20000000;RAM_PH_SIZE=0x40000;
                  FLASH_START=0x26000;FLASH_SIZE=0xda000;RAM_START=0x200022e0;RAM_SIZE=0x3dd20"
         31       linker_section_placements_segments="FLASH RX 0x0 0x100000;RAM RWX 0x20000000 0x40000"
    to: 
         30       linker_section_placement_macros="FLASH_PH_START=0x1000;FLASH_PH_SIZE=0xEF000;
                  RAM_PH_START=0x20000008;RAM_PH_SIZE=0x3FFF8;
                  FLASH_START=0x27000;FLASH_SIZE=0xb9000;RAM_START=0x200022e8;RAM_SIZE=0x3dd18"
         31       linker_section_placements_segments="FLASH RX 0x1000 0xEF000;RAM RWX 0x20000008 0x3FFF8"


nrf sdk 15.3.0 blinky
    same as 15.2.0: enable rtt log, disable uart log. it works the same. 


nrf cli usb log
https://github.com/jimmywong2003/nrf52840-dongle-example/


dongle uart log

  search nrf52840 pin 31
  https://devzone.nordicsemi.com/f/nordic-q-a/30194/default-pin-config-31-is-wrong-for-nrf52840
        default pin 31 for pwm is wrong
        NRF_DRV_PWM_DEFAULT_CONFIG value should be changed to NRF_DRV_PWM_PIN_NOT_USED

  default NRF_LOG_BACKEND_UART_TX_PIN is set to 31. will use 10 as in bookmark[3].
        #define RX_PIN_NUMBER   9 // 13
        #define TX_PIN_NUMBER  10 // 15

  the above is not worked out. eventually use a modified blinky example with uart1 on app.

