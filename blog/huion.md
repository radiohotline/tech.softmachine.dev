# Setting up my Huion Kamvas 13 on Arch Linux

First published: 2026/03/13

## Prerequisites:
**Tablet:** Huion Kamvas 13 2.5k QHD  
**Laptop:** Dell Inspiron 15-3567  
**OS:** Artix Linux running i3 (Xorg)

## The Problem:
Xorg's wacom drivers (`xf86-input-wacom`) don't work out of the box.

I will detail here the methods I tried to get my tablet up and running. While I highly suggest consulting the [Arch Linux Wiki](https://wiki.archlinux.org/title/Graphics_tablet), the information here may be useful to those troubleshooting the Kamvas 13 if the docs don't help.

## General steps to set up common graphics tablets on Linux (Xorg)

### Wacom

You're in luck, because `xf86-input-wacom` should work out of the box. However, you may need to set and edit buttons using the `xsetwacom` utility or via KDE's GUI program `wacomtablet`, which is part of the Plasma suite. Tablet configuration will be discussed below.
Alternatively, there is an XLibre fork of the drivers, which is named `xlibre-input-wacom` in the Arch repository.

### XP-Pen

Official Linux drivers exist for various distros, including Debian and derivatives, Fedora, Arch and derivatives, etc. In addition to .deb and .rpm packages, binaries are distributed, meaning they'd only work on a system with glibc.

### Huion

If you're using a Huion tablet, your first bet is on the official drivers. The official Huion tablet drivers are currently packaged for Debian-based distros, and I assume it should provide an identical interface to the Windows drivers. However, many of us don't use Debian-based distros. For Arch, there exists an AUR package of the drivers, but it is outdated at the time of writing. However, several alternatives exist, as outlined next.

### Open-source driver alternatives

Two notable open-source alternative driver projects that support several manufacturers are [OpenTabletDriver](https://opentabletdriver.net/) and [DIGImend](https://digimend.github.io/). I suggest you read their documentation for specific configurations.
OpenTabletDriver should be more straightforward to set up than the Xorg drivers, but I won't cover it in this article, as my tablet isn't supported by it.
DIGImend offers modules to be used in conjunction with the Xorg drivers, and working modules are pushed upstream to the Xorg Wacom drivers, meaning that these driver modules will only be necessary if your tablet is not supported by the Xorg modules yet. 

Note that any driver you install will override the Xorg one and any settings set by `xsetwacom`.

### What about Wayland?

I will not cover Wayland here, but information can be found on the Arch wiki [here](https://wiki.archlinux.org/title/Graphics_tablet#Wayland_and_libinput).

## My attempt

I am using the version of the tablet cable that comes with a separate input and power cable (plus HDMI), because my laptop does not have a USB-C port. I plug everything into the laptop.

If your tablet is a USB device, it should appear in the output of `lsusb` (from `usbutils`), as mine does:

	...  
	Bus 001 Device 049: ID 256c:006d  Huion Tablet_GT1302  
	...


At this stage, I had installed `xf86-input-wacom`. The tablet seems to wake up, but shuts off immediately, and that repeats endlessly. I had ruled out the issue being with the tablet, because it worked on other laptops running Windows. 

A quick `sudo dmesg | grep -i usb` shows that the connection is stable and not disconnecting over and over.

	[14449.733946] usb 1-1: new full-speed USB device number 33 using xhci_hcd  
	[14449.921838] usb 1-1: New USB device found, idVendor=256c, idProduct=006d, bcdDevice= 1.02  
	[14449.921902] usb 1-1: New USB device strings: Mfr=5, Product=6, SerialNumber=0  
	[14449.921941] usb 1-1: Product: Huion Tablet_GT1302  
	[14449.969508] input: Huion Tablet_GT1302 Pen as /devices/pci0000:00/0000:00:14.0/usb1/1-1/1-1:1.0/0003:256C:006D.0040/input/input185  
	[14450.025359] input: Huion Tablet_GT1302 Pad as /devices/pci0000:00/0000:00:14.0/usb1/1-1/1-1:1.0/0003:256C:006D.0040/input/input186  
	[14450.026896] input: Huion Tablet_GT1302 Touch Strip as /devices/pci0000:00/0000:00:14.0/usb1/1-1/1-1:1.0/0003:256C:006D.0040/input/input187  
	[14450.028279] input: Huion Tablet_GT1302 Dial as /devices/pci0000:00/0000:00:14.0/usb1/1-1/1-1:1.0/0003:256C:006D.0040/input/input188  
	[14450.031833] input: Huion Tablet_GT1302 Group Buttons as /devices/pci0000:00/0000:00:14.0/usb1/1-1/1-1:1.0/0003:256C:006D.0040/input/input189  
	[14450.033394] uclogic 0003:256C:006D.0040: input,hidraw4: USB HID v1.11 Keypad [Huion Tablet_GT1302] on usb-0000:00:14.0-1/input0  
	[14450.053384] input: Huion Tablet_GT1302 as /devices/pci0000:00/0000:00:14.0/usb1/1-1/1-1:1.1/0003:256C:006D.0041/input/input191  
	[14450.055795] uclogic 0003:256C:006D.0041: input,hidraw5: USB HID v1.11 Device [Huion Tablet_GT1302] on usb-0000:00:14.0-1/input1  
	[14450.080329] input: Huion Tablet_GT1302 Keyboard as /devices/pci0000:00/0000:00:14.0/usb1/1-1/1-1:1.2/0003:256C:006D.0042/input/input192  
	[14450.132366] input: Huion Tablet_GT1302 Consumer Control as /devices/pci0000:00/0000:00:14.0/usb1/1-1/1-1:1.2/0003:256C:006D.0042/input/input193  
	[14450.138729] input: Huion Tablet_GT1302 System Control as /devices/pci0000:00/0000:00:14.0/usb1/1-1/1-1:1.2/0003:256C:006D.0042/input/input194  
	[14450.143990] uclogic 0003:256C:006D.0042: input,hidraw6: USB HID v1.0b Keyboard [Huion Tablet_GT1302] on usb-0000:00:14.0-1/input2

Now this was an error on my end. My laptop cannot supply enough power, unlike my other one, so the solution was to connect the red cable (power cable) to a mobile charger adaptor.

Even with the now-stable connection, I do not see the expected behaviour, which is the tablet display mirroring my laptop screen. This behaviour was seen on my other laptop, even when running Arch with KDE. The solution to this was to manually set the display using `xrandr`. 

### The display

Check the output of `xrandr --listmonitors`:

	Monitors: 2  
	 0: +*eDP1 1366/340x768/190+0+0  eDP1  
	 1: +HDMI1 2560/677x1600/423+0+0  HDMI1

Here, eDP1 is my internal laptop screen, and HDMI1 is my tablet. If HDMI1 does not show up, make sure the HDMI cable is secure. If the cable is loose or the port is damaged, it may not show up immediately.

Next, we'll enable the display. If you prefer working with a GUI, you can use `ARandR`. In there, right click and select HDMI1 and click "Active," then arrange the displays as you like, setting your preferred resolution in the process. Note that not all resolutions work with the Kamvas 13.

Sometimes it suffices to use `xrandr --auto` or `xrandr --output HDMI1 --auto`.

If you place the displays next to each other, or some other configuration, the stylus range will be too large. This will be addressed in the input section below, and is fixed by mapping the stylus to your desired output (see #The stylus).

To mirror the laptop screen in the tablet display, run `xrandr --output HDMI1 --same-as eDP1 --auto`. This should be seamless if the displays have the same resolution. However, my screen is 1366x768 in size, so it shows up pretty tiny on the tablet. We'll have to use a scaling command.

If your laptop screen has a standard resolution like 1920x1080, you may use `xrandr --output HDMI1 --mode 1920x1080 --same-as eDP1 --auto`, but my screen needed the following to be mirrored well:

`xrandr --output eDP1 --mode 1366x768 --scale 1.874x2.083 --output HDMI1 --mode 2560x1600 --same-as eDP1`

...where the tablet's resolution is divided by the laptop's resolution. Very ugly solution, but the stylus maps perfectly.

To undo the scaling, use `xrandr --output eDP1 --scale 1x1`.

However, if you're like me, you may want to disable the laptop display as I do. In `ARandR`, just deactivate eDP1 by right-clicking and deselecting "Active." Via `xrandr`, try `xrandr --output eDP1 --off`. You can re-enable it swiftly with `xrandr --auto` or `xrandr --output eDP1 --auto --primary`. I recommend re-enabling it before you unplug the tablet, unless you have set up a working USB detection trigger with `udev`.

### The stylus

You must add a configuration file for Xorg to load the tablet driver modules. The DIGImend project has some sample configs. I referred to their [Huion sample config](https://github.com/DIGImend/digimend-kernel-drivers/blob/master/xorg.conf).

Create the file `/etc/X11/xorg.conf.d/10-tablet.conf` and add the following, where the value of `MatchUSBID` should be the ID you get from `lsusb`:

	Section "InputClass"  
		Identifier "Tablet"  
		Driver "wacom"  
		MatchDevicePath "/dev/input/event*"  
		MatchIsKeyboard "false"  
		MatchUSBID "256c:006d"  
	EndSection

Then restart X and check that the tablet is identified by `xsetwacom`. In my case, the output of `xsetwacom list devices` is the following:

	Huion Tablet_GT1302 Pen stylus          id: 18  type: STYLUS  
	Huion Tablet_GT1302 Touch Strip pad     id: 19  type: PAD  
	Huion Tablet_GT1302 Pad pad             id: 20  type: PAD  
	Huion Tablet_GT1302 Group Buttons pad   id: 25  type: PAD  
	Huion Tablet_GT1302 Dial pad            id: 26  type: PAD

Even with the display off, tapping and dragging with the pen should work, and it does. If it doesn't, check that the input is detected with `xinput list`:

	⎡ Virtual core pointer                          id=2    [master pointer  (3)]  
	⎜   ↳ Virtual core XTEST pointer                id=4    [slave  pointer  (2)]  
	⎜   ↳ Huion Tablet_GT1302 Group Buttons pad     id=25   [slave  pointer  (2)]  
	⎜   ↳ Huion Tablet_GT1302 Dial pad              id=26   [slave  pointer  (2)]  
	⎜   ↳ Huion Tablet_GT1302 Pad pad               id=20   [slave  pointer  (2)]  
	⎜   ↳ Huion Tablet_GT1302 Pen stylus            id=18   [slave  pointer  (2)]  
	⎜   ↳ Huion Tablet_GT1302 Touch Strip pad       id=19   [slave  pointer  (2)]  
	⎜   ↳ Huion Tablet_GT1302 Consumer Control      id=21   [slave  pointer  (2)]  
	⎜   ↳ Synaptics TM3096-006                      id=13   [slave  pointer  (2)]  
	⎣ Virtual core keyboard                         id=3    [master keyboard (2)]  
	    ↳ Virtual core XTEST keyboard               id=5    [slave  keyboard (3)]  
	    ↳ Video Bus                                 id=7    [slave  keyboard (3)]  
	    ↳ Power Button                              id=8    [slave  keyboard (3)]  
	    ↳ Power Button                              id=6    [slave  keyboard (3)]  
	    ↳ Sleep Button                              id=9    [slave  keyboard (3)]  
	    ↳ Huion Tablet_GT1302 System Control        id=24   [slave  keyboard (3)]  
	    ↳ Huion Tablet_GT1302 Consumer Control      id=22   [slave  keyboard (3)]  
	    ↳ Huion Tablet_GT1302 Keyboard              id=23   [slave  keyboard (3)]  
	    ↳ Intel HID events                          id=14   [slave  keyboard (3)]  
	    ↳ Dell WMI hotkeys                          id=15   [slave  keyboard (3)]  
	    ↳ AT Translated Set 2 keyboard              id=16   [slave  keyboard (3)]

If your tablet is detected, but it doesn't work, first test the stylus using `xinput test 18`, where `18` should be the ID you see for the Pen Stylus. If you move the pen around over the tablet, you'll see a lot of motion logging. Test the stylus buttons too.

Then, if the stylus works, but its input is not behaving as expected (for example, using the wrong display), map it with:

`xinput map-to-output [inputid] [display]`

or

`xsetwacom set [inputid] MapToOutput [display]`

where [inputid] is the stylus ID or name as found in `xsetwacom list devices` or `xinput list` and [display] is the desired display ID. If you use the name of the stylus (e.g. "Huion Tablet\_GT1302 Pen stylus"), don't forget to wrap it in quotations marks.

**But wait! None of this works!**

If nothing worked so far, try the DIGImend drivers. They're available in the AUR as `digimend-kernel-drivers-dkms`. After building them, run the following to unload the old tablet drivers used by Huion:

`sudo modprobe -r hid-kye hid-uclogic hid-polostar hid-viewsonic`

Reconnect the tablet, and the pen should work now. Repeat the above tests.

If the tablet isn't detected by your system, the issue might be with the USB port you used. Try other ones or wait for a bit, as there could be a delay. However, it is also possible that USB inputs are sleeping because of power-saving settings. If you're using `powertop`, that would be the reason. In `powertop`, head to `tunables` and set your tablet to `bad` to inhibit sleep.

#### Pen pressure

Pressure worked without further setup for me, but if you want to adjust pen pressure, check the Arch wiki.

### Button Configuration

Since this is a non-Wacom tablet, the GUI provided by KDE won't be useful. We'll have to use the CLI tool, `xsetwacom`. The process can be made reproducible by placing our commands in a script and (if you want) running the script on startup. I will attach my personal configuration after the explanation.

If your tablet is supported by OpenTabletDriver, it would be much easier to just use that, because it comes with a GUI.

Let's set up buttons with `xsetwacom` neatly.

A sample command from the DIGImend README shows this:
`xsetwacom set "HID 256c:006e Pad pad" button 9 key Ctrl Z`

In order to use it, we should know our button IDs. Run `xinput test "HUION Pen Tablet Pad pad"` or `xinput test 20`. The ID/name varies depending on your tablet's input method. You may have to use trial and error, but "Pad pad" should work for Kamvas 13. Press each button on your tablet, taking note of the output.

For example, if I press and release the topmost button, I get:

	button press   1 a[3]=0 a[4]=0 a[5]=0 a[6]=0  
	motion a[3]=0 a[4]=0 a[5]=0 a[6]=0  
	button release 1 a[3]=0 a[4]=0 a[5]=0 a[6]=0  
	motion a[3]=0 a[4]=0 a[5]=0 a[6]=0

and I note this down as "Button 1: 1", in the format of "Button [physical location]: [xinput output]" (you note the number following "press" or "release" as the ID).

My tests culminated into the following (buttons are counted from top to bottom):
>Button 1: 1  
>Button 2: 2  
>Button 3: 3  
>Button 4: 8  
>Button 5: 9  
>Button 6: 10  
>Button 7: 11

This is the expected output. The skipped numbers are reserved, as mentioned by the DIGImend README:
>Note that buttons are numbered 1, 2, 3, 8, 9, 10, and so on, i.e. buttons 4, 5, 6, and 7 are not used. They're reserved for vertical and horizontal scrolling events by the X server.

Additionally, I tested the stylus buttons again via `xinput test 18` and got the following in the mess of motion detection. I pressed each button multiple times to ensure I can catch it easily after ending the test process:
>Top button: 1  
>Bottom button: 3

Now, this overlap in numbers may seem problematic, but it is handled well in `xsetwacom`, as we can map them to different input keys.

At this stage, I start Krita in order to test the button shortcuts on the fly. You may use any program that has tablet support, or you may settle for good ol' `showkey -a` to quickly dump the key combinations you press. Since your tablet is identified as a keyboard of sorts, the output should appear here. Use your preferred method. To demonstrate output better, I will use `showkeys` here.

In ASCII, m is represented as `m`, Ctrl+M as `^M`, Alt+M as `^[m`, Shift+M as `M`, Ctrl+Shift+M as `^M`, and Ctrl+Alt+M as `^[M`. With this in mind, let us (finally) set some shortcuts.

To set the last button (button 11) to Ctrl+Z, run:
`xsetwacom set 20 button 11 key Ctrl Z`

A quick test in Krita works. `showkeys -a` output is `^Z       26 0032 0x1a`. Repeat the process for every button on your tablet. To see all supported key modifiers, run `xsetwacom list modifiers`.

My configuration, which is intended for Krita, is laid out as such:
>Button 1: Save (Ctrl+S)  
>Button 2: Mirror canvas (M)  
>Button 3: Zoom in (Ctrl+=)  
>Button 4: Pan (Space)  
>Button 5: Zoom out (Ctrl+-)  
>Button 6: Resize brush (by dragging) (Shift)  
>Button 7: Undo (Ctrl+Z)

My stylus buttons:
>Button 1: Eraser (E)  
>Button 2: Eyedropper (hold) (Ctrl)

And the final config script:

	#!/bin/sh  

	# Detect device IDs  

	for i in $(seq 10); do  
    		list=$(xsetwacom list devices)  
    		
		if echo "$list" | grep -q Huion; then  
        		break  
    		fi  
    		
		sleep 1 # waiting through startup time  
	done  

	list=$(xsetwacom list devices)  
	pad=$(echo "${list}" | awk '/Pad pad/{print $6; exit}')  
	stylus=$(echo "${list}" | awk '/Pen stylus/{print $6; exit}')  

	if [ -z "${pad}" ]; then  
		exit 0  
	fi  

	if [ -z "${stylus}" ]; then  
		exit 0  
	fi  

	# Tablet display + stylus

	#xrandr | grep -q "HDMI1 connected" || exit 0 # this is commented out because it's handled in the final i3 script

	xrandr --output eDP1 --mode 1366x768 --scale 1.874x2.083 --output HDMI1 --mode 2560x1600 --same-as eDP1

	xrandr --output eDP1 --off

	sleep 1

	xsetwacom set "$stylus" MapToOutput HDMI1

	# Functions

	setpad() { xsetwacom set "$pad" button "$1" "$2"; }  
	setstylus() { xsetwacom set "$stylus" button "$1" "$2"; }

	# Tablet buttons

	setpad 1 "key +ctrl s -ctrl"  
	setpad 2 "key m"  
	setpad 3 "key +ctrl = -ctrl"  
	setpad 8 "key +space"  
	setpad 9 "key +ctrl - -ctrl"  
	setpad 10 "key +shift"  
	setpad 11 "key +ctrl z -ctrl"  

	# Stylus buttons

	setstylus 2 "key e"  
	setstylus 3 "key +ctrl"

Since input device IDs often change across sessions, the script fetches the ID and uses them as variables. For that, I used the method from Arch Wiki and tweaked it as necessary.

## Automating the process

There are several ways to run custom processes at startup. The Arch Wiki article linked at the beginning details an approach for running the script when the tablet is connected. I use Artix with Runit, so I have to use a different `udev` that runs the script directly. This udev method didn't work for me. I'm not sure why, but I'm documenting it here anyway.
Unplugging the tablet will not automatically restore display settings, so add an event for device removal too:

	ACTION=="add", SUBSYSTEM=="usb", ATTRS{idVendor}=="256c", RUN+="/usr/local/bin/wacom-config.sh"  
	ACTION=="remove", SUBSYSTEM=="usb", ATTRS{idVendor}=="256c", RUN+="/usr/local/bin/reset-display.sh"

The display script:

	#!/bin/sh  

	export DISPLAY=:0  
	export XAUTHORIY=/home/troy/.Xauthority  

	xrandr --output HDMI1 --off --output eDP1 --auto --scale 1x1 --primary

In the end, I made the script run with i3, such that I can refresh my i3 session when I plug in the tablet:

	#!/bin/sh  

	export DISPLAY=:0  
	export XAUTHORITY=/home/troy/.Xauthority  

	if xrandr | grep -q "HDMI1 connected"; then  
        	/home/troy/wacom-config.sh  
	else  
        	xrandr --output HDMI1 --off  
        	xrandr --output eDP1 --auto --scale 1x1 --primary  
	fi
