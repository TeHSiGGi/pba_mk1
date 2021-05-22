# PBA MK1

## Introduction

The **P**ortable **B**luetooth **a**mp MK1 is the first iteration of a portable bluetooth amplifier.

The goal was to design a universal base platform for designing bluetooth speaker in a range of small to large power.

The amplifier is meant to be powered by LiPo batteries.

## Technical specification

### Power Supply

The power supply provides different voltages for digital and analog purpose.

It provides the following rails:
- `D33` - 3.3V 0.25A Digital - for digital logic and bluetooth module
- `A75` - 7.5V 0.10A Analog - for analog audio processing
- `D120` - 12V 1.00A Digital - Control voltage for Class-D amplifier
- `VAMP` - VBAT - 30V 3.00A - Amplification voltage for Class-D amplifier

The `D33` rail is constantly enabled and provides power for the bluetooth module.

All other rails are enabled on demand by the bluetooth module, via the `EN` signal.

As regulators for all rails expect `VAMP` `LMR50410` are used. 

The `VAMP` rail can be varied between VBAT and 30V. This is provided by using a `LM5122` boost converter, that will be enabled on demand. If not required, the `LM5122` will go into passthrough mode and disable the boost action. The boost action is controlled via a separate `LVL` signal.

The power supply also provides the capability to charge and use a 4S LiPo battery. Please note: The power supply does **not** provide any balancing or protection circuitry. Those things must be included in your 4S LiPo battery pack.

As charging controller a `LTC4006-6` is used, which incorporates all necessary functionality.
The power supply used to charge the LiPo battery is a 19V external power supply. The input current is limited to 3.07A.

Charging current is limited to 2.00A and the charging timer is configured to approx. 2 hours.

There is currently **no** NTC readout configured in the charging circuit.

As a charger supply, I recommend the use of generic laptop chargers with 19V and 3.54A - they are widely available, reliable and cheap.

### Amplifier and DAC

As amplifiers there are two `TPA3245` used in this project.

The first of them is driven by directly by the `CSR8675` bluetooth chip DAC outputs, using a BTL configuration for two output channels.

The second amplifier is configured in a PBTL configuration and is driven by a `PCM5102` DAC.

The first amplifier should be used for full-range or dedicated mid/migh-range speakers, whereas the second amplifier with its PBTL configuration is meant to drive low-range/subwoofer speakers, since due to its configuration it is able to deliver more current and drive speakers with low impedances.

All analog signals are used differentially to reduce noise and hum. Between the differential DAC output and amplifier inputs there are `TS914` adding amplification to the signals for additional volume.

The amplifiers will automatically be enabled, if music is playing. This is controlled by the `CSR8675` using the `AMP_EN` signal.

There is an additional level detection circuit that will switch the amplifier supply voltage by setting the `LVL` signal to `high`. This allows the amplifier chips to run at a lower voltage for moderate volumes and have enough headroom for high volume music playback. The usage of a higher voltage also allows for better use of higher impedance speakers, such as 8-16Ohms.

The threshold of the level detection can be configured via a potentiometer on the amplifier board.

### Bluetooth

As for bluetooth, the `CSR8675` in form of `BTM-875` modules is used.

That chip provides the following functionality:
- Bluetooth 4.0+ compliance
- Stereo differential analogue audio inputs (used as line-in)
- Stereo differential analogue audio outputs (driving `TPA3245` in BTL configuration)
- I²S output (driving `PCM5102` for second `TPA3245` in PBTL configuration)
- LED and Button GPIOs (3 LEDs and 8 GPIOs are exposed)
- GPIO control for enabling external circuitry (enabling power with `EN` and amplifiers with `AMP_EN`)
- Kalimba DSP (used for crossover and DSP audio processing)
- True Wireless Stereo (can link two amplifiers together playing music in sync)

Software used on the `CSR8675` is configured using the ADK in version 4.1.35.