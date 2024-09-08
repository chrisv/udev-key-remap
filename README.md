# udev-key-remap

This is a set of remap-files to remap the `capslock` key to `esc` via udev. (58 is the decimal code for the capslock key if you're wondering.)

Tested/used with F40 (Fedora Workstation 40) running (directly) on Lenovo hardware (P52/P720).

## Why?

I switched to udev because per login/session `setxkbmap` no longer worked after upgrading to F40.

...But why remap `capslock` to `esc`? I use [vim motions](https://vimhelp.org/motion.txt.html) a lot, which implies using the `esc` key a lot. The `capslock` key is a key I almost never use, so I remap it to `esc` to not have to hyperextend my pinky and risk RSI. (A similarly practical substitution for emacs users to keep their left hand from transforming into a vampyric claw is mapping the `leftctrl` key to the `capslock` key, which is probably also why HHKB put it there on their keyboards.)

## Warning: this is system-wide

With `setxkbmap` remaps are ad hoc, i.e. per login or creation of session (i.e. via `/etc/profile` or `$HOME/.profile`), so any other users (e.g. root, others) were not affected.

```sh
# custom keyboard settings which worked via pre-F40 $HOME/.profile 
# -- make CAPSLOCK behave like ESC
# setxkbmap -option caps:escape
# -- clear any existing options:
# setxkbmap -option
```

In contrast, a udev key remap is system-wide, so all users are affected, including root. This is is because [libevdev does not have knowledge of X clients or Wayland clients, it is too low in the stack](https://www.freedesktop.org/software/libevdev/doc/latest/).
This is a feature, not a bug, but do be aware of this and only install on personal/single-use workstations/laptops and ssh from there into all other envs that are either shared or should not be affected by your personal key remaps.

## Install

Copy the required `.hwdb` files to `/etc/udev/hwdb.d/` and run:

```sh
sudo systemd-hwdb update
sudo udevadm trigger
```

Note: the `58-AT_Translated_Set_2_keyboard.hwdb` file can be used for most, if not all, built-in laptop keyboards because they typically use the same device identifier (`b0011v0001p0001*`). Any external keyboard (wired, bluetooth, whatever) will most likely need to be added (see below) because udev key remaps are device specific.

## Filenames

The `.hwdb` files are named after the device name as shown in `evtest` (see below for more info).

## Adding another keyboard

Example below is for creating one for the `Lenovo ThinkPad Compact USB Keyboard with TrackPoint` (`event6` connects to the keyboard, `event7` to the trackpoint).

Notice there is another keyboard `AT Translated Set 2 keyboard` seemingly attached as well, but this refers to the built-in laptop keyboard. Same procedure applies if you want to remap keys on that keyboard, but you can use the `58-AT_Translated_Set_2_keyboard.hwdb` file for that (it's generic for most, if not all built-in laptop keyboards).

1. run `evtest`
2. select the device event number for the *keyboard* event (the keyboard may have multiple device *events*, e.g. trackpoint, mouse, or custom programmable buttons, etc.)
3. write down the *Input device ID* (below it's `bus 0x3 vendor 0x17ef product 0x6047 version 0x100`)
4. write down the *Input device name* (below it's `"Lenovo ThinkPad Compact USB Keyboard with TrackPoint"`)
5. press the capslock key (or any other key you want to remap, e.g. you could also remap esc to capslock to still have capslock functionality, or have capslock be backspace, etc etc)
6. write down the value for the key (below it's `70039` for capslock)
7. Press Ctrl-C to interrupt (you may want to press capslock again as it's not yet remapped and you're now typing in captials)
8. copy an existing `.hwdb` file as template and replace with where applicable with the values found (see below for info on how to specify the *Input device ID* in the file)

```sh
# if not yet installed: sudo dnf install evtest
sudo evtest
# No device specified, trying to scan all of /dev/input/event*
# Available devices:
# /dev/input/event0:      Sleep Button
# /dev/input/event1:      Lid Switch
# /dev/input/event10:     ThinkPad Extra Buttons
# /dev/input/event11:     HDA NVidia HDMI/DP,pcm=7
# /dev/input/event12:     HDA NVidia HDMI/DP,pcm=8
# /dev/input/event13:     HDA NVidia HDMI/DP,pcm=9
# /dev/input/event14:     Elan Touchpad
# /dev/input/event15:     Elan TrackPoint
# /dev/input/event16:     HDA Intel PCH Mic
# /dev/input/event17:     HDA Intel PCH Headphone
# /dev/input/event18:     HDA Intel PCH HDMI/DP,pcm=3
# /dev/input/event19:     HDA Intel PCH HDMI/DP,pcm=7
# /dev/input/event2:      Power Button
# /dev/input/event20:     HDA Intel PCH HDMI/DP,pcm=8
# /dev/input/event21:     SteelSeries Arctis Nova 7
# /dev/input/event3:      AT Translated Set 2 keyboard
# /dev/input/event4:      PC Speaker
# /dev/input/event5:      HDA NVidia HDMI/DP,pcm=3
# /dev/input/event6:      Lenovo ThinkPad Compact USB Keyboard with TrackPoint
# /dev/input/event7:      Lenovo ThinkPad Compact USB Keyboard with TrackPoint
# /dev/input/event8:      Video Bus
# /dev/input/event9:      Video Bus
Select the device event number [0-21]: 6
# Input driver version is 1.0.1
# Input device ID: bus 0x3 vendor 0x17ef product 0x6047 version 0x100
# Input device name: "Lenovo ThinkPad Compact USB Keyboard with TrackPoint"
# Supported events:
#   Event type 0 (EV_SYN)
#   Event type 1 (EV_KEY)
#     Event code 1 (KEY_ESC)
#     Event code 2 (KEY_1)
#     Event code 3 (KEY_2)
#     Event code 4 (KEY_3)
#     Event code 5 (KEY_4)
#     Event code 6 (KEY_5)
#     Event code 7 (KEY_6)
#     Event code 8 (KEY_7)
#     Event code 9 (KEY_8)
#     Event code 10 (KEY_9)
#     Event code 11 (KEY_0)
#     Event code 12 (KEY_MINUS)
#     Event code 13 (KEY_EQUAL)
#     Event code 14 (KEY_BACKSPACE)
#     Event code 15 (KEY_TAB)
#     Event code 16 (KEY_Q)
#     Event code 17 (KEY_W)
#     Event code 18 (KEY_E)
#     Event code 19 (KEY_R)
#     Event code 20 (KEY_T)
#     Event code 21 (KEY_Y)
#     Event code 22 (KEY_U)
#     Event code 23 (KEY_I)
#     Event code 24 (KEY_O)
#     Event code 25 (KEY_P)
#     Event code 26 (KEY_LEFTBRACE)
#     Event code 27 (KEY_RIGHTBRACE)
#     Event code 28 (KEY_ENTER)
#     Event code 29 (KEY_LEFTCTRL)
#     Event code 30 (KEY_A)
#     Event code 31 (KEY_S)
#     Event code 32 (KEY_D)
#     Event code 33 (KEY_F)
#     Event code 34 (KEY_G)
#     Event code 35 (KEY_H)
#     Event code 36 (KEY_J)
#     Event code 37 (KEY_K)
#     Event code 38 (KEY_L)
#     Event code 39 (KEY_SEMICOLON)
#     Event code 40 (KEY_APOSTROPHE)
#     Event code 41 (KEY_GRAVE)
#     Event code 42 (KEY_LEFTSHIFT)
#     Event code 43 (KEY_BACKSLASH)
#     Event code 44 (KEY_Z)
#     Event code 45 (KEY_X)
#     Event code 46 (KEY_C)
#     Event code 47 (KEY_V)
#     Event code 48 (KEY_B)
#     Event code 49 (KEY_N)
#     Event code 50 (KEY_M)
#     Event code 51 (KEY_COMMA)
#     Event code 52 (KEY_DOT)
#     Event code 53 (KEY_SLASH)
#     Event code 54 (KEY_RIGHTSHIFT)
#     Event code 55 (KEY_KPASTERISK)
#     Event code 56 (KEY_LEFTALT)
#     Event code 57 (KEY_SPACE)
#     Event code 58 (KEY_CAPSLOCK)
#     Event code 59 (KEY_F1)
#     Event code 60 (KEY_F2)
#     Event code 61 (KEY_F3)
#     Event code 62 (KEY_F4)
#     Event code 63 (KEY_F5)
#     Event code 64 (KEY_F6)
#     Event code 65 (KEY_F7)
#     Event code 66 (KEY_F8)
#     Event code 67 (KEY_F9)
#     Event code 68 (KEY_F10)
#     Event code 69 (KEY_NUMLOCK)
#     Event code 70 (KEY_SCROLLLOCK)
#     Event code 71 (KEY_KP7)
#     Event code 72 (KEY_KP8)
#     Event code 73 (KEY_KP9)
#     Event code 74 (KEY_KPMINUS)
#     Event code 75 (KEY_KP4)
#     Event code 76 (KEY_KP5)
#     Event code 77 (KEY_KP6)
#     Event code 78 (KEY_KPPLUS)
#     Event code 79 (KEY_KP1)
#     Event code 80 (KEY_KP2)
#     Event code 81 (KEY_KP3)
#     Event code 82 (KEY_KP0)
#     Event code 83 (KEY_KPDOT)
#     Event code 85 (KEY_ZENKAKUHANKAKU)
#     Event code 86 (KEY_102ND)
#     Event code 87 (KEY_F11)
#     Event code 88 (KEY_F12)
#     Event code 89 (KEY_RO)
#     Event code 90 (KEY_KATAKANA)
#     Event code 91 (KEY_HIRAGANA)
#     Event code 92 (KEY_HENKAN)
#     Event code 93 (KEY_KATAKANAHIRAGANA)
#     Event code 94 (KEY_MUHENKAN)
#     Event code 95 (KEY_KPJPCOMMA)
#     Event code 96 (KEY_KPENTER)
#     Event code 97 (KEY_RIGHTCTRL)
#     Event code 98 (KEY_KPSLASH)
#     Event code 99 (KEY_SYSRQ)
#     Event code 100 (KEY_RIGHTALT)
#     Event code 102 (KEY_HOME)
#     Event code 103 (KEY_UP)
#     Event code 104 (KEY_PAGEUP)
#     Event code 105 (KEY_LEFT)
#     Event code 106 (KEY_RIGHT)
#     Event code 107 (KEY_END)
#     Event code 108 (KEY_DOWN)
#     Event code 109 (KEY_PAGEDOWN)
#     Event code 110 (KEY_INSERT)
#     Event code 111 (KEY_DELETE)
#     Event code 113 (KEY_MUTE)
#     Event code 114 (KEY_VOLUMEDOWN)
#     Event code 115 (KEY_VOLUMEUP)
#     Event code 116 (KEY_POWER)
#     Event code 117 (KEY_KPEQUAL)
#     Event code 119 (KEY_PAUSE)
#     Event code 121 (KEY_KPCOMMA)
#     Event code 122 (KEY_HANGUEL)
#     Event code 123 (KEY_HANJA)
#     Event code 124 (KEY_YEN)
#     Event code 125 (KEY_LEFTMETA)
#     Event code 126 (KEY_RIGHTMETA)
#     Event code 127 (KEY_COMPOSE)
#     Event code 128 (KEY_STOP)
#     Event code 129 (KEY_AGAIN)
#     Event code 130 (KEY_PROPS)
#     Event code 131 (KEY_UNDO)
#     Event code 132 (KEY_FRONT)
#     Event code 133 (KEY_COPY)
#     Event code 134 (KEY_OPEN)
#     Event code 135 (KEY_PASTE)
#     Event code 136 (KEY_FIND)
#     Event code 137 (KEY_CUT)
#     Event code 138 (KEY_HELP)
#     Event code 183 (KEY_F13)
#     Event code 184 (KEY_F14)
#     Event code 185 (KEY_F15)
#     Event code 186 (KEY_F16)
#     Event code 187 (KEY_F17)
#     Event code 188 (KEY_F18)
#     Event code 189 (KEY_F19)
#     Event code 190 (KEY_F20)
#     Event code 191 (KEY_F21)
#     Event code 192 (KEY_F22)
#     Event code 193 (KEY_F23)
#     Event code 194 (KEY_F24)
#     Event code 240 (KEY_UNKNOWN)
#   Event type 4 (EV_MSC)
#     Event code 4 (MSC_SCAN)
#   Event type 17 (EV_LED)
#     Event code 0 (LED_NUML) state 1
#     Event code 1 (LED_CAPSL) state 0
#     Event code 2 (LED_SCROLLL) state 0
#     Event code 3 (LED_COMPOSE) state 0
#     Event code 4 (LED_KANA) state 0
# Key repeat handling:
#   Repeat type 20 (EV_REP)
#     Repeat code 0 (REP_DELAY)
#       Value    250
#     Repeat code 1 (REP_PERIOD)
#       Value     33
# Properties:
# Testing ... (interrupt to exit)
# Event: time 1725230763.045901, type 17 (EV_LED), code 0 (LED_NUML), value 0
# Event: time 1725230763.045901, type 4 (EV_MSC), code 4 (MSC_SCAN), value 70028
# Event: time 1725230763.045901, type 1 (EV_KEY), code 28 (KEY_ENTER), value 0
# Event: time 1725230763.045901, -------------- SYN_REPORT ------------
# Event: time 1725230764.829896, type 4 (EV_MSC), code 4 (MSC_SCAN), value 70039
# Event: time 1725230764.829896, type 1 (EV_KEY), code 58 (KEY_CAPSLOCK), value 1
# Event: time 1725230764.829896, -------------- SYN_REPORT ------------
# Event: time 1725230764.830345, type 17 (EV_LED), code 0 (LED_NUML), value 1
# Event: time 1725230764.830345, type 17 (EV_LED), code 1 (LED_CAPSL), value 1
# Event: time 1725230764.830345, -------------- SYN_REPORT ------------
# Event: time 1725230764.949909, type 4 (EV_MSC), code 4 (MSC_SCAN), value 70039
# Event: time 1725230764.949909, type 1 (EV_KEY), code 58 (KEY_CAPSLOCK), value 0
# Event: time 1725230764.949909, -------------- SYN_REPORT ------------
# Event: time 1725230773.381863, type 4 (EV_MSC), code 4 (MSC_SCAN), value 700e0
# Event: time 1725230773.381863, type 1 (EV_KEY), code 29 (KEY_LEFTCTRL), value 1
# Event: time 1725230773.381863, -------------- SYN_REPORT ------------
# Event: time 1725230773.635969, type 1 (EV_KEY), code 29 (KEY_LEFTCTRL), value 2
# Event: time 1725230773.635969, -------------- SYN_REPORT ------------
# Event: time 1725230773.669975, type 1 (EV_KEY), code 29 (KEY_LEFTCTRL), value 2
# Event: time 1725230773.669975, -------------- SYN_REPORT ------------
# Event: time 1725230773.704018, type 1 (EV_KEY), code 29 (KEY_LEFTCTRL), value 2
# Event: time 1725230773.704018, -------------- SYN_REPORT ------------
# Event: time 1725230773.738066, type 1 (EV_KEY), code 29 (KEY_LEFTCTRL), value 2
# Event: time 1725230773.738066, -------------- SYN_REPORT ------------
# Event: time 1725230773.772983, type 1 (EV_KEY), code 29 (KEY_LEFTCTRL), value 2
# Event: time 1725230773.772983, -------------- SYN_REPORT ------------
# Event: time 1725230773.807971, type 1 (EV_KEY), code 29 (KEY_LEFTCTRL), value 2
# Event: time 1725230773.807971, -------------- SYN_REPORT ------------
# Event: time 1725230773.841968, type 1 (EV_KEY), code 29 (KEY_LEFTCTRL), value 2
# Event: time 1725230773.841968, -------------- SYN_REPORT ------------
# Event: time 1725230773.876012, type 1 (EV_KEY), code 29 (KEY_LEFTCTRL), value 2
# Event: time 1725230773.876012, -------------- SYN_REPORT ------------
# Event: time 1725230773.910007, type 1 (EV_KEY), code 29 (KEY_LEFTCTRL), value 2
# Event: time 1725230773.910007, -------------- SYN_REPORT ------------
# Event: time 1725230773.943983, type 1 (EV_KEY), code 29 (KEY_LEFTCTRL), value 2
# Event: time 1725230773.943983, -------------- SYN_REPORT ------------
# Event: time 1725230773.978037, type 1 (EV_KEY), code 29 (KEY_LEFTCTRL), value 2
# Event: time 1725230773.978037, -------------- SYN_REPORT ------------
# Event: time 1725230774.012040, type 1 (EV_KEY), code 29 (KEY_LEFTCTRL), value 2
# Event: time 1725230774.012040, -------------- SYN_REPORT ------------
# Event: time 1725230774.021894, type 4 (EV_MSC), code 4 (MSC_SCAN), value 700e0
# Event: time 1725230774.021894, type 1 (EV_KEY), code 29 (KEY_LEFTCTRL), value 0
# Event: time 1725230774.021894, -------------- SYN_REPORT ------------
# Event: time 1725230774.357908, type 4 (EV_MSC), code 4 (MSC_SCAN), value 700e0
# Event: time 1725230774.357908, type 1 (EV_KEY), code 29 (KEY_LEFTCTRL), value 1
# Event: time 1725230774.357908, -------------- SYN_REPORT ------------
# Event: time 1725230774.477950, type 4 (EV_MSC), code 4 (MSC_SCAN), value 70006
# Event: time 1725230774.477950, type 1 (EV_KEY), code 46 (KEY_C), value 1
# Event: time 1725230774.477950, -------------- SYN_REPORT ------------
# ^C
```

(above is a bit verbose, but it's to show you what you can expect; you can ignore most of it.)

## Converting the *Input device ID*

In the .hwdb file the device ID needs to be specified as follows:

```
evdev:input:b${bus}v${vendor}p${product}*
```

The `bus`, `vendor` and `product` need to be 0-prepadded to 4 long and any characters need to be UPPERCASED, and the *version* should be ignored and replaced by a `*` to indicate *any version*, e.g.:

```
# Input device ID: bus 0x3 vendor 0x17ef product 0x6047 version 0x100
# becomes:
evdev:input:b0003v17EFp6047*
```

## Example .hwdb file

```
### remaps capslock to esc via udev for the keyboard below
### -- cf. https://github.com/chrisv/udev-key-remap
###
### Input device name: "Topre REALFORCE 87 US"
### Input device ID: bus 0x3 vendor 0x853 product 0x145 version 0x111
###
### Manufacturer: Topre Corporation  (Japan)
### Model: R2TL-US5-IV/AHBZ52 (TKL-87-US-ANSI)
###
evdev:input:b0003v0853p0145*
  KEYBOARD_KEY_70039=esc
```

## Windows?

Yeah... no. But if you install a systemd-distro in WSL2 it *might* work. Have not tried (yet).

## Author

[Chris Vertonghen](https://github.com/chrisv)

## Refs / thanks

- <https://www.kernel.org/doc/html/latest/input/event-codes.html#input-event-codes>
- <https://fedoraproject.org/wiki/Features/EvdevInputDriver>

## License

MIT. No guarantees, no warranties. Use at your own risk.
