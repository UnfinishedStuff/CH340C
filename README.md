# Using the CH340C with the 328P

This is a quick writeup of how to use the WCH CH340C with the ATMega328p, just for future reference.  This guide details how to wire the CH340C with a 328p on a breadboard so that the 328 can be programmed over USB as if it was an Arduino Uno.

# Why the CH340C?

The 328p is the chipt at the heart of the Arduino Uno, but it doesn't have a native USB connection, which is useful for easily uploading new programs to it.  This means that another chip is required to program it and pass serial data.  In the Uno this is done by another ATMega microcontroller, the 16u2.  Why the 16u2 was chosen I don't know, but these chips are moderately expensive (~£2) and a bit more complex than is really necessary.  Usually an FTDI FT232R would be used instead.  However, these chips cost somewhere in the region of £3+ from Farnell, and cheap chips from less well known etailers can be counterfeit.  Sometimes these don't function properly, and sometimes FTDI [decides to break them](https://hackaday.com/2016/02/01/ftdi-drivers-break-fake-chips-again/) to arm-twist people into buying genuine chips.

After "FTDI Gate" there were a lot of discussions about alternatives, and the CH340G by WCH was brought up.  It was cheap, and in hobby circles that often takes precedence over a lot of things.  Unfortunately, the CH340G needs an external crystal to function, which the FT232R doesn't, and that makes a circuit a bit more complex.  Fortunately, WCH seem to have acknowledged that this isn't ideal, and have released a series of chips which perform the same function with the oscillator built-in.  In particular, the CH340**C**, which is a SOIC-16 shaped chip for ~£0.30 each from [LCSC](https://lcsc.com/product-detail/USB_CH340C_C84681.html).

At the time of writing the datasheet for the CH340C has a very nice pinout diagram, but otherwise is entirely in Chinese.  Which makes it a bit unhelpful.  After getting one and messing around with it a bit, here is how I've gotten it to work:

![CH340C diagram](https://github.com/Shoe-Pi/CH340C/Circuit_diagram.jpg "CH340C diagram")

The VCC capacitors are taken straight from the datasheet.  The datasheet for the FT232 recommends a ferrite bead on 5V from the USB port and a capacitor across the USBs 5V and GDN rails, but no such requirements for the CH340C.

The one thing which I had issues with is that the 0.1 uF capacitor on the V3 pin is **required**.  I was under the impression that this was a 3v3 output, but although I can't read the description of it in the CH340C datasheet the FT232 sheet notes that this is mainly for providing 3v3 to the chip itself for certain functions.  Without this capacitor a Windows PC doesn't recognise the USB device.

TX and RX on the CH340C need to be swapped over and connected directly to the RX and TX of the 328p, respectively.  DTR should be connected to the 328p's Pin 1/Reset through a 100nf capacitor, which is apparently important for making sure the 328p doesn't restart too soon after being sent the reset signal.

And that's it!  Most of the pins are for other functions which aren't required.  Under this setup a 328p chip on a breadboard should be recognised and programmed as if it was an Arduino Uno.
