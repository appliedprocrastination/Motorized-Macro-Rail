# Motorized Macro Rail Hardware
## Summary
PCB-design for controlling a motorized macro rail. Designed for photographers with electronics skills rather than engineers with photography skills. Main features:
- Zero surface mount components, and should therefore be possible to hand solder - even for people with novice soldering skills. 
- Flexible solution for connecting to most types of camera via 2.5mm stereo jack, pin header, or solder joints.
- Simple design for driving a single motor via a BTT TMC2209 V1.2 motor driver.
- Based on the WiFi-enabled NodeMCU 32S V2 DevKit. 
- Has broken out headers for connecting limit switches and rotary encoders (neither of which are used in the intended application the board was designed for).
- Total cost of around $30 (Camera, optics, motor, slider, and tools not included)

![PerspectiveView](./Outputs/Images/MacroRail-Option-A-3D-Perspective.png) 

## Configurability
The design is quite adaptable in general, and there are few components that are completely necessary for functioning (see the section "Minimum viable design"). There is however one part of the circuit that is particularly adaptable, and requires the users attention to guarantee a working configuration: the shutter control circuit - which depends on what kind of camera you are planning to control with this setup.

Snippet of schematic (DNP = Do Not Place):
![ShutterCircuitSchematic](./Outputs/Images/Documentation/Shutter-circuit.png)

As shown in the image above, there is a lot going on for what is normally a very simple circuit (you can trigger many cameras by simply short-circuiting all the pins in the remote shutter connector with a piece of wire). The reason for all this additional complexity is to keep the design as flexible as possible, and hopefully allow the PCB to be used with any type of camera. This is obviously not possible for me to test out in person, so I'm basing most of what follows on [this blog-post by Günter from LRTimelapse](https://lrtimelapse.com/news/intervalometer-hack/) (which is based on feedback from a solid photographer community) and private conversations with Stefan from [CNCKitchen](https://www.youtube.com/CNCKitchen) (as he owns a camera not mentioned in the above blog-post).

All the extra nick-nack is an attempt to solve the following design-requirements:
1. Different cameras require different signals to trigger
    - Example: 
        - Canon requires Shutter and GND to be shorted
        - Nikon/Sony requires Shutter, Focus and GND to be shorted
        - Lumix requires a [resistor network](https://i1.wp.com/fluxing.de/wp-content/uploads/2018/03/arduino-panasonic-lumix-shutter-schematic.jpg) between Shutter and GND.
2. Different cameras may swap the layout of Shutter and Focus signals on the 2.5mm jack
3. The user may not own a connector with a 2.5mm jack, so a pin header/2.54mm pitch solder joint option is also provided.
4. The high currents drawn by the motor may cause noise on the PCB, so therefore the shutter circuit is optically isolated from the rest of the electronics to avoid unintended triggering of the camera shutter.

The following components are layed out in the design solely to handle requirements 1. and 2.
- SJ1, SJ2, SJ3
- R2, R3 (Note: These are, to my knowledge, only required by Lumix - and should not be mounted by default)

The solder jumpers (SJ1-3) are there to give the user an opportunity to make permanent (but recoverable) changes to the circuit. The way this works is that each SJ consists of two exposed copper pads with a small trace connecting the pads. Cutting the trace with a scalpel will remove the connection between the pads, but it is still possible to recover this connection by applying a bit of solder as a bridge between the pads (in case the user buys a new camera, or just cut the wrong wire).

### An example of how to use solder jumpers:
Suppose your camera starts updating the screen each time the autofocus-signal is activated (even in manual focus mode), so you want to stop that signal from being activated each time a picture is captured. In this scenario, you can use a scalpel to break the wire connecting the two pads of SJ2 by cutting along the red arrow in the following picture: 
![How-to-cut-SJ2](./Outputs/Images/Documentation/cut_trace.png)

### Default configuration (Canon,Nikon,Sony):
The default configuration requires you to not mount R2 and R3. The pads on all the solder jumpers are shorted by default, meaning that a simplified schematic of the circuit looks like this:
![Default-config](./Outputs/Images/Documentation/Illustration-of-shutter-circuit-default.png)
This should work for most Canon, Nikon, and Sony cameras.

### Lumix configuration:
To use the shutter circuit with a Lumix camera, the resistor network must be mounted (R2 and R3). Keep in mind that SJ1 is shorted by default, meaning that it needs to be cut in order for R2 to take any effect.

Mount R2 and R3, then use a scalpel to break the wire in SJ1 by cutting along the red arrow in this picture.
![Lumix-config-modification](./Outputs/Images/Documentation/Lumix_config.png)
Cutting this wire will activate R2 and leave you with the following schematic:

![Lumix-config-schematic](./Outputs/Images/Documentation/Illustration-of-shutter-circuit-Lumix.png)

## Layout Options
In the folder named Outputs/Gerber you will find design files that can be sent to a PCB manufacturer for production. There are two options, A and B, which differ only in the orientation of the power connector. The reason that there are two options is to accommodate for mounting the PCB on different tripod designs. Keep in mind that the PCB is mounted on a moving part of the macro rail, so protruding parts may get stuck or bent by the force from the motor.

The design files are labelled Option A and Option B, and this illustration circles their differences.

![Option A vs. Option B](./Outputs/Images/Documentation/Option-A-vs-B-2.png)

## Bill Of Materials (BOM)
*Most of the AliExpress links are affiliate links. Prices are estimates and may vary with your location (shipping cost)*

### On PCB:
 - [PCB itself](./Outputs/Gerber/MacroRail_V1_0-2022-02-26.zip). Ca $10 at JLCPCB
 - 1x [TMC2209-V1.2](https://s.click.aliexpress.com/e/_9uc1XB) Stepper motor driver ([Documentation](https://github.com/bigtreetech/BIGTREETECH-TMC2209-V1.2/)). ca $8
 - 1x [NodeMCU 32s V2](https://www.aliexpress.com/item/1005001636295529.html) Dev kit ([Documentation](https://docs.ai-thinker.com/en/esp32/boards/nodemcu_32s)) ca $5
 - 1x [PC817 Optocoupler](https://s.click.aliexpress.com/e/_AAgoAp) ca $1
 - 1x [100 uF capacitor (35V)](https://s.click.aliexpress.com/e/_ADnqKh) ca $1
 - 1x [10 uF capacitor (25V)](https://s.click.aliexpress.com/e/_ADnqKh) ca $1
 - 1x [1 uF capacitor](https://s.click.aliexpress.com/e/_9j2jOz) ca $1
 - 1x [2.1 mm Barrel Jack (power)](https://s.click.aliexpress.com/e/_Aq01NT) ca $1
 - 1x [Selection of resistors](https://s.click.aliexpress.com/e/_AOqGqt) (depending on your camera) ca $1
    - Note: R1 and R4 must always be mounted (these are 600 Ohm and 1k Ohm resistors respectively)
 - 1x [SJ1-2503A 2.5 mm stereo jack receptacle (for shutter cable)](https://no.mouser.com/ProductDetail/CUI-Devices/SJ1-2503A?qs=WyjlAZoYn52728cbIH3aBA%3D%3D) ca $1 (optional)
 - 1x [4-pin screw terminal block](https://s.click.aliexpress.com/e/_97dipR) ca $2 (optional)
 - 1x [TIP120 transistor (for powering external LED lighting)](https://s.click.aliexpress.com/e/_ApnAYv) ca $1 (optional) 
 - 3x [3-pin pin row](https://s.click.aliexpress.com/e/_ABvcat) ca $1 (optional)
 - 1x [4-pin pin row](https://s.click.aliexpress.com/e/_ABvcat) ca $1 (optional)
 - 1x [2-pin pin row](https://s.click.aliexpress.com/e/_ABvcat) ca $1 (optional)
 - 2x [19-pin pin header](https://s.click.aliexpress.com/e/_AFHrY5) ca $2 (optional)
 - 2x [8-pin pin header](https://s.click.aliexpress.com/e/_AFHrY5) ca $1 (optional)
 - 1x [2-pin pin header](https://s.click.aliexpress.com/e/_AFHrY5) ca $1 (optional)

 Total on PCB: ca $40 ($29 when excluding optional components)

### Outside of PCB:
Mandatory parts:
 - [Macro Rail](https://s.click.aliexpress.com/e/_9IiOvB) ca. $20 
 - [Nema 14 high torque motor](https://s.click.aliexpress.com/e/_ArX3ot) (High torque may be unnecessary for moving compact cameras) ca $20
 - RMS threaded microscope lens (focused to 160mm). [Example](https://s.click.aliexpress.com/e/_9zATGv) ca $8
 - 5-12V Power supply (current can be limited with TMC2209 if 5V is too much for your motor). [Example](https://s.click.aliexpress.com/e/_AC81D7) ca $8 
 - Cable for shutter release that fits your camera (you can either use the 2.5mm jack receptacle or cut the cable and solder it directly to the PCB via JP1)
    - [Example for various cameras](https://s.click.aliexpress.com/e/_9iNl7J) ca $3

Parts that can be 3D-printed if you design something that suits your camera:
 - Bellow spacer that fits your camera (with converters to hold the microscope lens). This is optional, and can be 3D printed instead. 
    - Examples for Canon 5D:
        - [Bellow spacer](https://s.click.aliexpress.com/e/_A5xnYt) ca $40
        - [EFS to M24 converter](https://s.click.aliexpress.com/e/_9QvErF) ca $5
        - [M42 to RMS converter](https://s.click.aliexpress.com/e/_9QPTOd) ca $5
 
Total outside of PCB: ca $109 ($59 when excluding parts that can be 3D-printed)

## Preview

### Top
![TopView](./Outputs/Images/MacroRail-Option-A-3D-Top.png) 
### Bottom
![BottomView](./Outputs/Images/MacroRail-Option-A-3D-Bottom.png)
### Minimum viable design
Note: You may need to mount more resistors to get a minimum viable design (depending on your camera)
![MinimumViable](./Outputs/Images/Documentation/Minimalist-setup.png)

### Various documentation:
- [NodeMCU-32S Core Development Board](https://docs.ai-thinker.com/en/esp32/boards/nodemcu_32s)
- [NodeMCU Arduino Core (Software)](https://github.com/espressif/arduino-esp32)
- [TMC2209-V1.2 manual (Motor driver module)](https://github.com/bigtreetech/BIGTREETECH-TMC2209-V1.2/blob/master/manual/TMC2209-V1.2-manual.pdf)
- [TMC2209 datasheet (chip itself)](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC2209_datasheet_rev1.07.pdf)