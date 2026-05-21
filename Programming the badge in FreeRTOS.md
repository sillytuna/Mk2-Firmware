The following content is extracted from the official TiLDA MKe FreeRTOS documentation [^1].

# Programming the badge in FreeRTOS

## Your first “Hello world” app

There’s a “HelloWorldApp.cpp” file in which you can play around. In order for it to show up on the Homescreen you have to uncomment line 51 in AppManager.cpp and flash the changed code to the badge. Great app pull requests are appreciated!

If you are still using the Arduino IDE at this point, note that it will not let you edit the .cpp and .h files that are needed to create Apps for the badge. To force the IDE to re-compile/re-read any files you've edited using an external editor, make sure to go to the File -> Preferences dialog box, and check the "Use external editor" checkbox.

## Why are things so different from standard Arduino code?

We’re using a library called FreeRTOS that allows us to multitask - something that’s normally not possible with standard Arduino code. This allows us to run multiple tasks at the same time. FreeRTOS uses preemptive scheduling to switch between the task. Due to this we have to be very careful about how we do some things. For example we can’t just define interrupts for buttons in every task (imagine the mess!) or write to the serial port directly (your task might stop in the middle of the message).

We’ve also spent quite a lot of time to make the built-in components as easy to use as possible without having every task to write lots of boilerplate code. If you feel like using the build-in components on the badge, chances are we already wrote a wrapper for them that is already used by one of the other tasks.

Have a look at the “Documentation” section in this document for a full list of API functions. You will avoid a lot of headaches if you stick to those.

## Code structure

- FreeRTOS has the concept of “Tasks” which work like threads. We’ve wrapped them in a class called “Task” (for background stuff) and “Apps” (for foreground, one-at-a-time things)
- Everything needs to be in the main EMF2014 folder. Subfolders are not allowed. This is an Arduino IDE restriction :(

## Debugging and Gotchas

- The USB serial is set up to 115200 baud. There are lots of terminals that can connect to them. See below for how to enable the debug logging.
- If you can’t revive a badge, or only get the two red programming LEDs when you plug it, in, do a full erase (see below)
- Avoid busy waiting, use FreeRTOS queues and Tilda::delay() instead
- Don’t use low level functions like interrupts or serial ports directly unless you really, really know how FreeRTOS will handle them. For general logging you can use Tilda::log()
- If sending code to the badge using the Arduino IDE "Upload" button fails, even though the /dev/ttyACM0 (linux com port) is there, just retry, twice if necessary.

## Full erase

This is the failsafe process if your badge won't show up over USB.

1. Unplug the badge
2. Connect the Erase pins on the back together (two holes down the left hand side next to the battery and under the blue wireless module). You can use a jumper, jump wire, or the leg of a resistor for this.
3. Turn the badge back on with the switch or plug it back in
4. Press the Reset button on the front (bottom centre) and release
5. Wait 15 seconds
6. Unplug the badge or turn it off

When you plug the badge back into a computer it will come back up in programming mode, with a different serial port to the usual one. Open the EMF 2014 sketch in the Arduino IDE, select the Tilda v0.333 (RTOS) programmer, and find the new serial port. The IDE console should show something like this:

```text
Sketch uses 118,748 bytes (22%) of program storage space. Maximum is 524,288 bytes.
Erase flash
Write 127668 bytes to flash

[                                ] 0% (0/499 pages)
[                                ] 2% (10/499 pages)
[=                               ] 4% (20/499 pages)
[=                               ] 6% (30/499 pages)
...
[============================= ] 98% (490/499 pages)
[==============================] 100% (499/499 pages)
Verify 127668 bytes of flash

[                                ] 0% (0/499 pages)
[                                ] 2% (10/499 pages)
[=                               ] 4% (20/499 pages)
[=                               ] 6% (30/499 pages)
...
[============================= ] 98% (490/499 pages)
[==============================] 100% (499/499 pages)
Verify successful
Set boot flash true
CPU reset.
```

[^1]: [Programming the badge in FreeRTOS - EMF Badge Documentation](https://badge.emfcamp.org/TiLDA_MKe/FreeRTOS/) (100%)
