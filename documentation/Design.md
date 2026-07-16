# Osprey EVR FMC Design Goals

## High level specifications
### Jitter Cleaner
- 2+ LVDS outputs
- Controlled by 2.5V logic from the FPGA (to avoid level shifting)
- Frequencies of interest are 100MHz-250MHz

### Digital I/O
- 16+ outputs with a couple of inputs
- Some/all outputs can drive 50ohm
- Better than 20ps of jitter on clocked digital outputs

# Osprey EVR FMC Part Selection

## Clock Jitter Cleaner Selection

### Skyworks Parts
#### Si5315/16/17/19 - 300pS jitter
- These parts are quite simple and similar
- 3rd Gen DSPLL
- They have a PPL Bypass mode which could be nice for testing.
- CON - these parts seem optimized to operate at fixed tables of frequencies, but will work at 125MHz
Out of this bunch the Si5317A/B seems like it best matches our needs. has 125MHz +/-5MHz options.
These are available form Mouser and Digikey for $15-$20

#### Si5342-45 - 90pS jitter
- 4th Gen DSPLL
- Recommended DSPLLs for Line Card by Skyworks: [link](https://www.skyworksinc.com/-/media/Skyworks/SL/documents/public/application-notes/an1077-selecting-clocks-for-timing-synchronization.pdf)
 - Con - may need level shifting for SPI bus
Out of this bunch the Si5342B seems like it best matches our needs.
These are available form Mouser and Digikey for $26
#### Si5392-95 - 75pS jitter
- This part was used by Paul B. in another similar role but is a clock gen
- 4th Gen DSPLL
- A slightly updated Si534* part with similar combabilities (Drop-in compatible) but options for integrated XTAL
Either Si5392 or Si5394 with a A or B speed grade
These are available form Mouser and Digikey for $35-40
### TI Parts
#### LMK04100 family ~ 200pS jitter [datasheet](https://www.ti.com/lit/ds/symlink/lmk04111.pdf?ts=1769610888824)
- Simple, cheap ($<10), not readily in stock
- require external loop filter (a bit harder to design board for)
- LMK04131
#### LMK04000 family ~ 150pS jitter [datasheet](https://www.ti.com/lit/ds/symlink/lmk04033.pdf?ts=1769572954419)
- require external loop filter (a bit harder to design board for)
- LMK04033 seems like an ok part
### Analog Parts
 - Parts with dedicated jitter cleaning available are quite complicated, and try to integrate more than just jitter cleaning.

## Directly Clocked outputs
There was an idea to clock the Digital I/O directly from the jitter cleaner rather than feeding it back to the FPGA.

- Thinking through this option controlling phase of the clock (from IC) and data (from FPGA) may be an issue.
		- Not sure if this an issue as it could be calibrated out in a consistent way
		- It may be harder to sync outputs from 2 nodes


## SI5392 Setup
- [datasheet](https://www.skyworksinc.com/-/media/Skyworks/SL/documents/public/data-sheets/si5395-94-92-a-datasheet.pdf)
- [Si5395/94/92 Any-Frequency, Any-Output, Jitter-Attenuators/Clock Multipliers Family Reference Manual](https://www.skyworksinc.com/-/media/Skyworks/SL/documents/public/reference-manuals/si5395-94-92-family.pdf)
- [Recommended Crystal, XO, TCXO, and OCXO Reference Manual for High-Performance Jitter Attenuators and Clock Generators](https://www.skyworksinc.com/-/media/Skyworks/SL/documents/public/reference-manuals/si534x-8x-9x-recommended-crystals-rm.pdf)

### Choosing a XTAL
- Looked at high initial tolerance (10ppm or below) parts at 48MHZ from the chart
- Selected Kyocera CX3225SB48000D0F PJC1 based on availability

### Power Supplies
- section 14 in reference manual mentions a few things
	- It is recommended to use a 1 μF 0402 ceramic capacitor on each VDD for optimal performance. It is also suggested to include an optional, single 0603 (resistor/ferrite) bead in series with each supply to enable additional filtering if needed

	- Four classes of supply voltages exist on the Si5395/94/92:
		- VDD = 1.8 V (Core digital supply)
		- VDDA = 3.3 V (Analog supply)
		- VDDOx = 1.8/2.5/3.3 V ± 5% (Clock output supply)
		- VDDS = 1.8/3.3 V ± 5% (Digital I/O supply)
	- I will level shifter for VDDS based IO to FPGA?

- M2C avoid becuase it goes through mux
- check for CM requirments of output of chip to input of fpga line (both GBT and CC)
	- .3 - 1.5V CM


A clean 3.3V is preferred (for the analog core) over the 3.3V from Marble

1st approach is 12V -> ~4.5V ->(LDO) 3.3V

Switcher
both are some of the most available part/ are simple/ and will work
Diodes Inc - AP62200TWU-7
TI - TPS56x200

LDO to 3.3V

# Digital Outputs
## Goals 
- Low Output Jitter
	- 20pSec 
- 50ohm drive capable LVTTL?

## Comparable
MRF EVR
	- 4x "programmable front panel TTL outputs"
	- 3x "differential CML pattern outputs capable of RF recovery"
	- jitter typically < 15 ps rms for TTL outputs, < 5 ps rms for CML outputs
Universal IO
	 - LVTTL Output
		 - Seem to use RF amp
	 - LVTTL Output with delay tuning
		 - Seems to use some LVDS/LVPECL/CML delay IC ex:NB6L295
			 - fine tunes pulse in 10s pico-sec increments

## Our Outputs
Currently using FPGA GPIO / LVCMOS
 - Kintex7 switching datasheet [link](https://docs.amd.com/v/u/en-US/ds182_Kintex_7_Data_Sheet)
	 - Table 20?
- Use weak-drive LVCMOS outputs to appox 50ohm drive pg. 46 [UG483](https://docs.amd.com/v/u/en-US/ug483_7Series_PCB)
- potential issues/sources of jitter with SE
	- FPGA supply noise coupling into GPIO threshold
	- Receiver threshold noise
	- Ground bounce on single-ended outputs (*more a problem with clocks than our pulses)
- Using LVDS/diff outputs should improver these at the cost of complexity

## Signal Path options
signal
### Diff Receivers
https://www.ti.com/product/DS90LV028A0 
DS90LV048 - quad

### Op-amp to drive 50ohm
ADA4311
AD8009
### Comparators - excellent jitter but for CML output
https://www.analog.com/media/en/technical-documentation/data-sheets/ADCMP604_605.pdf
TLV3601
LMH7220
LTC6752
ADCMP580


### LVDS Buffer/Dist IC's to move to a front panel
https://www.ti.com/product/DS90LV004
https://www.ti.com/product/DS25BR440
https://www.ti.com/product/DS15BR401
https://www.ti.com/product/DS15BR400
https://www.ti.com/product/DS10BR254
https://www.ti.com/product/SCAN90004

## Powering Digital Outputs
Have a separate switched rail to power this
- 1 output needs 99.4mA if shorted; 49.8mA under normal conditions
- Split power into 2x 8Ch sections
- Possibly isolate the comparators too.

Low dropout LDO options
- qualities
	- 1A output
	- fixed (ideal)
	- low dropout of less than 500mV
	- Available on Digikey/Mouser 
-Looked at
- TPS737
- TPS7A91
- TPS7A94
- TPS7A80
- TPS759P
- TLV796
- TLV759


# Board Stackup
Board stackup and Impedance

F - CLK
1 - GND
2 - LVDS
3 - GND
4 - PWR
5 - MISC& Power Previously 2 and 4
6 - GND
B -MISC

External Layers
	- .21mm Trace/.2 Width
		- Gives 106 on Kicad with Zeven 68
		- Gives 94 on Saturn with Zeven 63; Zo 54
Interal Layers
- .15mm Trace/.25 Width
	- - Gives 94.5 on Saturn with Zeven 54; Zo 50.5

Old Layer stackup - For note
1 - Z0
2 -GND
3 - in2  -pwr
4 -in3 -lowspeed
5 -GND
6 - Z0