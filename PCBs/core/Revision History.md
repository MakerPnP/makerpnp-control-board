# Rev A1

## Errata

### issue with MCU and FPGA activity LEDs.

On initial power up, the MCU and FPGA activity LEDS, are sometimes bright, sometimes faint, but never off.

No workaround / solution developed yet.

### FPGA starts on power-up.

After some consideration, the FPGA should NOT start up on power-up, but only after the MCU drives CRESET_B high.
Currently there is a pull-up on CRESET_B which makes the FPGA load it's program from flash on boot.

#### Workaround

Reposition R107, remove it from the board, stand it on it's side, and place it diagonally between pad C148:2 (GND) and pad R107:2 (CRESET_B). Pad R107:1 (V3P3/VCCIO_2) should not have any component connected to it.

This will change the pull-up to a pull-down and prevent the FPGA from booting up when power is applied. The MCU now has
to drive CRESET_B high and keep it high to make the FPGA boot.

### FPGA does not boot from flash.

The FPGA waits in SPI slave mode to be programmed, instead of booting from flash as originally intended.

This was due to a misunderstanding of the FPGA documentation due to bad wording in the hardware checklist manual.

#### Workaround

Remove R131, move it between pad R107:1 (which will be empty after applying the other workaround above) and pad J405:13.

This will make the FPGA boot from flash when the CRESET_B pin is pulled high by the MCU.


### Power supervisor prevents regulators from starting up.

The power supervisor (TPS3820-50DBVx) prevents the regulators from starting up.

#### Workaround

a) Bypass it by cutting the PGOOD trace on the bottom of board then jumper C2:1 to U2:1 

or

b) replace with a TPS3820-33DBVx which has a lower threshold.

## Problems

### ESP-C6 module not working.

The ESP-C6 modules did not reflow properly, many of the pins were unconnected.  Likely less paste is needed under the
four corner pads and under the center pads so that the module sits lower to the board.

#### Workaround

1. Ensure SD card and platic connectors are covered with heat-resistant tape.
2. Remove the module.
3. Inspect the pads on the module, take photo.
4. remove the high-temperature solder from the board and module using solder wick.
5. Apply flux to the module, tin the IO pads on the module with low-temperature solder, but not the thermal pads 
   or the 4 corner pads.
6. Apply solder to the module IO pads on the PCB, but not the thermal pads or the 4 corner pads.
7. Reflow the module, note it will self center strongly, ensure it floats back into position.
8. Allow the solder to cool and solidify brielfly.
9. Press down on the module and reflow it, being careful not to let it move.
10. Allow the solder to cool and solidify, then remove pressure.
11. Power on the board, hold down ESP BOOT button, connect USB cable. Ensure USB devices are enumerated.
12. If not replace the module for a new one and re-test.

# Rev B1

## Changes from Rev A1

* Use TPS3820-33DBVx instead of TPS3820-50DBVx for the power supervisor. 
* Changed the pull-down on SPI_SS_B to a pull-up so the FPGA default behavior is to boot from flash when reset.
  Note: The MCU can still drive SPI_SS_B low, then set CRESET_B high so make the FPGA wait in SPI slave mode.
* Changed the Paste for the ESP-C6 module
  * Only center pad has paste, but 15% fill
  * Corner pads have the same volume of paste as the signal pads. 40% fill.
  * All module solder pads have defined paste settings, previously they were all default.
* Changed the silkscreen outline of the ESP-C^ module so it's easier to confirm alignment.
* Changed the pull-up on CRESET_B to a pull-down to keep the FPGA disabled until the MCU turns it on.

## TODO

### CRITICAL

[ ] don't fit R1 V5EN by default

### Would-be-nice

[ ] fix the MCU and FPGA activity LEDs so they are not randomly ON or dim by default.  perhaps by adding pull-downs
    to the FPGA output pins.
[ ] add a warning triangle and 'see manual' next to the V5EN resistor jumper.
