BIN = output/ble_uart.bin
IHEX = output/ble_uart.hex
ELF = output/ble_uart.elf
ASM = output/ble_uart.asm

ROOT := ../../../..
PROJ_ROOT := ..

VPATH = $(PROJ_ROOT)
VPATH += $(PROJ_ROOT)/Source

#include $(ROOT)/components/gcc/components.cflags
#include $(ROOT)/components/gcc/components.mk

include bleUart_components.cflags
include bleUart_components.mk

DEF += -DMAX_NUM_LL_CONN=1
DEF += -DGATT_MAX_NUM_CONN=MAX_NUM_LL_CONN+1

FLASH_LD = $(PROJ_ROOT)/gcc/bleUart.ld

#OTA src
DEF += -DPHY_SLB_OTA_ENABLE=1 -DCFG_CP -DPHY_6222 -DBLEUART_AT -DMTU_SIZE=247 -DAT_CMD -DOSAL_SNV_UINT16_ID -DCFG_QFN32 -DCFG_SLEEP_MODE=PWR_MODE_NO_SLEEP -DHOST_CONFIG=4 -DHCI_TL_NONE=1 -DENABLE_LOG_ROM_=0 -DPHY_MCU_TYPE=MCU_BUMBEE_M0 -DMAX_NUM_LL_CONN=1 -DGATT_MAX_NUM_CONN=MAX_NUM_LL_CONN+1
INC   += -I$(ROOT)/components/profiles/ppsp
VPATH += $(ROOT)/components/profiles/ppsp
CSRCS += ppsp_impl.c
CSRCS += ppsp_serv.c
CSRCS += list_slst.c
CSRCS += core_queu.c

#project src
SRC_RAW = main.c
SRC_RAW += OSAL_bleuart.c
SRC_RAW += peripheral.c
SRC_RAW += led.c
SRC_RAW += cliface.c
SRC_RAW += bleuart.c
SRC_RAW += bleuart_service.c
SRC_RAW += bleuart_protocol.c
SRC_RAW += bleuart_Main.c
SRC_RAW += bleuart_at_dma.c
SRC_RAW += bleuart_at_cmd.c

STARTUP_RAW = phy6222_start.s

SRC_RAW += $(CSRCS)

INC += -I$(PROJ_ROOT)/source
INC += -Ioutput

CFLAGS += -O0
CFLAGS += -g3
CFLAGS += --specs=nosys.specs
CFLAGS += -mcpu=cortex-m0
#CFLAGS += -mfloat-abi=soft
CFLAGS += -mthumb
CFLAGS += -ffunction-sections
#CFLAGS += -mfpu=fpv4-sp-d16
CFLAGS += -Wall
#CFLAGS += --specs=rdimon.specs


#LFLAGS += -mcpu=cortex-m0
#LFLAGS += -mfloat-abi=soft
#LFLAGS += -mthumb
#LFLAGS += -mfpu=fpv4-sp-d16
LFLAGS += -Wl,--gc-sections
LFLAGS += -Wl,-Map=output/build.map
LFLAGS += $(ROOT)/misc/bb_rom_sym_m0.gdbsym
LFLAGS += $(LIBS_PATH)
LFLAGS += $(LIBS)

OBJ = $(SRC_RAW:%.c=%.o)
OBJ :=  $(notdir $(OBJ))

STARTUP_OBJ = $(STARTUP_RAW:%.s=%.o)
STARTUP_OBJ :=  $(notdir $(STARTUP_OBJ))

OBJ_OUT := $(patsubst %,output/%,$(OBJ))
STARTUP_OBJ_OUT := $(patsubst %,output/%,$(STARTUP_OBJ))

all: pre-build $(OBJ) $(STARTUP_OBJ) build

build:
	arm-none-eabi-gcc -o $(ELF) $(CFLAGS) $(OBJ_OUT) $(STARTUP_OBJ_OUT) $(LFLAGS) -T$(FLASH_LD) 
#arm-none-eabi-ld -o $(ELF) $(LFLAGS) $(OBJ) $(STARTUP_OBJ) -T$(FLASH_LD) 
	arm-none-eabi-objcopy -O binary -S $(ELF) $(BIN)
	arm-none-eabi-objcopy -O ihex $(ELF) $(IHEX)
#arm-none-eabi-objdump -s -S $(ELF) > $(ASM)



pre-build:
	@echo $(STARTUP_OBJ)
	@echo $(OBJ)
	@echo $(BIN)
	mkdir -p output

%.o:%.s
	arm-none-eabi-gcc -c $(CFLAGS) $< -o output/$@
	@echo

%.o:%.c
	arm-none-eabi-gcc -c $(CFLAGS) $(DEF) $(INC) $< -o output/$@
	@echo

.PHONY: clean
clean :
	rm -rf output
