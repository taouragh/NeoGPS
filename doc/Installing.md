Installing
==========
**1.**  Download the [master ZIP file](https://github.com/SlashDevin/NeoGPS/archive/master.zip).

You can extract/unzip the files for use in step 3, but \*\***DO NOT**\*\* copy these files into your `Arduino/Libraries` folder!  This is different from most libraries.

Unfortunately, the sad state of Arduino library management requires you to copy the NeoGPS files into *each* example subdirectory that you would like to try.  :(  Fortunately, this will allow you to have a different configuration for each example and sketch that you write. :)

For most non-Mega boards (e.g., UNOs) and GPS devices that run at 9600, 19200 or 38400 baud, you should also download and install the [NeoSWSerial](https://github.com/SlashDevin/NeoSWSerial) library.  NeoSWSerial files should be copied to your `Arduino/Libraries` folder, like most Arduino libraries.
<br>
<br>

**2.**  Starting with the first example program, `NMEA.ino`, copy this subdirectory into your main Arduino directory:
```
NeoGPS-master/examples/NMEA  ->  Arduino
```
This creates an `Arduino/NMEA` sketch subdirectory containing `NMEA.INO`.
<br>
<br>

**3.**  Copy all the .H and .CPP files from the top directory of NeoGPS-master into `Arduino/NMEA`.  Your application subdirectory should now contain these files:
```
    CosaCompat.h
    DMS.cpp
    DMS.h
    GPSfix.h
    GPSfix_cfg.h
    GPSport.h
    Location.h
    Location.cpp
    NMEA.ino
    NMEAGPS.cpp
    NMEAGPS.h
    NMEAGPS_cfg.h
    NeoGPS_cfg.h
    Streamers.cpp
    Streamers.h
    Time.cpp
    Time.h
```
You do not need the files from any other subdirectories, like **ublox**.  Most of the example programs only use these generic NMEA files.
<br>
<br>

**4.** Review the example `GPSport.h` to confirm that the correct serial port will be used for your GPS device.

By default, Mega Boards will use `Serial1`.  If you have installed the [NeoHWSerial](https://github.com/SlashDevin/NeoHWSerial) library and included the header before `GPSport.h`, then `NeoSerial1` will be used.

For all other Boards, a software serial instance will be created on pins 3 and 4.  If your GPS is on different pins, put these `#define` lines in the INO, before the `#include "GPSport.h"` line:

    #define RX_PIN 2
    #define TX_PIN 3
    #include "GPSport.h"

All the example programs can use any of the following serial port types:

* [NeoSWSerial](https://github.com/SlashDevin/NeoSWSerial) (default, works on most pins)
* [NeoICSerial](https://github.com/SlashDevin/NeoICSerial) (only works on one specific Input Capture pin)
* [AltSoftSerial](https://github.com/PaulStoffregen/AltSoftSerial) (only works on one specific Input Capture pin)
* SoftwareSerial (built-in, not recommended)

To select one of the non-default types, simply include their header before including `GPSport.h`:

    //#include <NeoHWSerial.h>
    #include <NeoICSerial.h>
    //#include <NeoSWSerial.h>
    //#include <SoftwareSerial.h> /* NOT RECOMMENDED */
    #include "GPSport.h"

The above will cause `GPSport.h` to declare `gps_port` using the class `NeoICSerial`.

Modify these defaults if necessary, or if you know what serial port to use, you can declare it yourself.  Be sure to delete the line `#include "GPSport.h"`, and delete the file `GPSport.h`.
<br>
<br>
**5.**  Start the IDE by double-clicking on the `NMEA.INO` file and upload the example sketch.

**Note:**  If the sketch does not compile, please see the [Troubleshooting](Troubleshooting.md#configuration-errors) section.

When the sketch begins, you should see this:
```
NMEA.INO: started
  fix object size = 31
  gps object size = 84
Looking for GPS device on Serial1

GPS quiet time is assumed to begin after a RMC sentence is received.
  You should confirm this with NMEAorder.ino

Status,UTC Date/Time,Lat,Lon,Hdg,Spd,Alt,Sats,Rx ok,Rx err,Rx chars,
3,2016-05-24 01:21:29.00,472852332,85652650,,138,,,1,0,66,
3,2016-05-24 01:21:30.00,472852311,85652653,,220,24040,7,9,0,557,
3,2016-05-24 01:21:31.00,472852315,85652647,,449,24080,7,17,0,1048,
  etc.
```
The default NeoGPS configuration is **Nominal**, as described [here](Configurations.md#typical-configurations).  This output can be copy & pasted into a spreadsheet for graphing or analysis, or into a text editor for saving as a CSV file.

If you do not see this output, please review the  [Troubleshooting](Troubleshooting.md#gps-device-connection-problems) section.

#The NMEA.ino example works!
Once you have verified the GPS device connection and build process with this first example, you should also verify your device's behavior with `NMEAorder.ino` (see [this section](Troubleshooting.md#quiet-time-interval)).  This can avoid problems later on, when you start adding/merging other functions to do your "work".

[Other examples](Examples.md) include `NMEAloc.ino`, which shows how to use just the location fields of a fix, or `NMEAtimezone.ino`, which shows how to adjust the GPS time for your local time zone.

If you are logging information to an SD card, you should next try `NMEA_isr.ino`.  It is identical to `NMEA.ino`, except that it handles the GPS characters during the RX char interrupt.  Interrupt handling will require one of the NeoXXSerial libraries to be installed (e.g. [NeoHWSerial](https://github.com/SlashDevin/NeoHWSerial)).

If you are working on a drone or other autonomous system, you should then try `NMEAcoherent.ino`.  Although this is a polling example, you should eventually use the interrupt-driven technique.

You can also try other configurations.  Please see [Choosing Your Configuration](Choosing.md) for more information, and then simply edit `GPSfix_cfg.h` and/or `NMEAGPS_cfg.h`, or select an [example configuration](../configs) and copy these three files into your application directory: `NeoGPS_cfg.h`, `GPSfix_cfg.h`, and `NMEAGPS_cfg.h`.

You can review and edit each of the copied configuration files to add or remove messages or fields, at any time.

**Note:**  Not all configurations will work with all example applications.  Compiler error messages are emitted for incompatible settings, or if an example requires certain configurations.

#I have a ublox GPS device
After you have tried all the standard NMEA examples, and you need the ublox-specific capabilities of NeoGPS, please see the [ublox](ublox.md) section.  Try `PUBX.ino` first, then try `ublox.ino` if you *really* need the binary protocol.
