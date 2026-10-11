Orpheus

A desk buddy built on an ESP32-C3 Super Mini with a 1.3" OLED face. Orpheus boots with a splash screen, then settles into an animated face that blinks.

This is the first stage of a longer project: the plan is for Orpheus to grow into a voice assistant ("Hey Orpheus, turn on the living room light") with an LLM behind it.

Status: work in progress — Phase 1 (face and animation)

Features
Boot splash — "ORPHEUS" centred on screen for 5 seconds at power-on
Animated face — two eyes and a smile, drawn from bitmaps
Smooth blink — a 4-frame close/open sequence with a short hold at the bottom
Squash and stretch — the mouth rises 1 px per frame as the eyes close and drops back as they open, so the whole face moves with the blink
Hardware
Part	Notes
ESP32-C3 Super Mini	Native USB (USB-Serial/JTAG), no separate USB-to-serial chip
1.3" OLED, 128×64	SH1106 driver, I2C, monochrome (white)
Breadboard + jumper wires	Header pins on the ESP32 must be soldered
Wiring
OLED pin	ESP32-C3 Super Mini pin
VCC	3.3
GND	G
SCK (I2C clock / SCL)	GPIO 9
SDA	GPIO 8

In code: Wire.begin(8, 9); // SDA = 8, SCL = 9

Software
Arduino IDE 2
ESP32 board package (Espressif)
Libraries (install via Library Manager):
Adafruit SH110X
Adafruit GFX (installed as a dependency)
Graphics designed in Lopaka and exported as Adafruit GFX bitmaps
Board settings
Board: ESP32C3 Dev Module
USB CDC On Boot: Enabled (needed for Serial Monitor output)
Port: whichever COM port the board appears on
Getting started
Wire the OLED as shown above.
Open orpheus.ino in Arduino IDE.
If the upload fails or the board keeps disconnecting, put it into download mode: hold BOOT, tap RST, release BOOT, then upload.
Press RST after uploading to run the sketch.
How the animation works

The display library draws into a buffer in memory. Nothing appears on screen until display.display() sends the whole buffer over I2C. Each animation frame is:

clearDisplay()
draw the eyes and mouth for that frame
display()
delay() before the next frame

The blink plays frames 1 → 4 (closing), holds briefly, then 3 → 1 (opening). Each eye frame is vertically centred on the same point, so the eyes close towards the middle rather than collapsing upwards.

Troubleshooting notes

Lessons learned getting the hardware working:

Board keeps connecting/disconnecting over USB: the ESP32-C3's USB lives inside the chip, so a crash or boot loop drops the USB connection. Entering download mode (BOOT + RST) and flashing a known-good sketch fixes it. The CH340/CH341 driver is not needed for this board.
Arduino IDE auto-selects the wrong board: every ESP32-C3 reports the same USB ID, so pick ESP32C3 Dev Module manually.
I2C scanner finds nothing, or the screen drops in and out: check the header pins are soldered. Pins pushed through the holes make unreliable contact.
"SCK" on the pinout diagram vs "SCK" on the OLED: the diagram's SCK (GPIO 4) is for SPI. The OLED's SCK is the I2C clock (SCL), which goes to GPIO 9.
Screen is found but stays blank: this module uses an SH1106, not an SSD1306. Use the Adafruit SH110X library.
Roadmap
 Boot splash
 Static face
 Blink animation
 Random blink timing
 Eyes look around
 Refactor blink frames into a single parameterised draw function
 Battery power
 RGB display
 Voice assistant with LLM integration ("Hey Orpheus")
