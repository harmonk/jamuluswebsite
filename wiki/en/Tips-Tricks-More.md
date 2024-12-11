---
layout: wiki
title: "Tips, Tricks and More"
lang: "en"
permalink: "/wiki/Tips-Tricks-More"
---

# Tips & Tricks
 {:.no_toc}

<details markdown="1">

<summary>Table of contents</summary>

* TOC
 {:toc}

</details>

###  Learning about remote band rehearsing

Jamulus user [Chris Rimple](https://sourceforge.net/u/chrisrimple/profile/) has compiled a massive amount of information relating to [Remote Band Rehearsals](https://docs.google.com/document/d/1smcvsxdaaViPQvGMQHmah_6BQeqowhmGSFMHfnlY2FI/) (Google doc), which covers topics such as hardware and software configuration including examples and advice for newcomers to the field. He also includes a section on Jamulus in comparison to other solutions.

## Using the Jamulus Client

###  Have an undisturbed session on any Server

You can have an undisturbed session with other people on any Server by simply soloing each other. You will then not be able to hear anyone else if they enter your Server.

**NOTE:** This does not stop people from hearing you, using the chat function, or seeing your profile information.

###  Using Jamulus audio in Zoom (or other) meeting apps

Several users have reported success allowing a "virtual audience" for a Jamulus session by using [JACK audio](https://jackaudio.org) to route the Jamulus signal through JackRouter to the target application (in this case, Zoom meetings).

You can also use [VoiceMeeter](https://www.vb-audio.com/Voicemeeter/banana.htm) (Banana) for Windows or [BlackHole](https://github.com/ExistentialAudio/BlackHole) for macOS to route the Jamulus output to multiple destinations, for example to your headphones and the meeting application at the same time.

### Recording Jamulus on Windows with Reaper

Jamulus user [Rob Durkin](https://sourceforge.net/u/bentwrench/profile/) has written a [guide to recording the output of Jamulus](https://docs.google.com/document/d/1tENfNKTWHasuTg33OdLLEo4-OOhWJolOo42ffSARxhY/edit) (Google Doc) using the ReaRoute add-on for [Reaper](https://www.reaper.fm/).

### Sharing song/chord sheets

Jamulus user [BTDT](https://sourceforge.net/u/btdt/profile/) has written a system called [305keepers](https://github.com/keepers305/Song-Sheet-Sharing-Web-Pages), a web application that allows a "Jam leader" to push song sheets (in PDF format) to "Jammers" in real time using standard web browsers.

### Jamulus Client Linux start script

Here's a Linux start script for Jamulus using an old Audigy4 sound card, the large number of available audio faders for which makes it hard to get the correct settings.

This script therefore includes the most important audio fader settings. The second part of the script deals with the JACK connections. I use Guitarix as my guitar effect processor which I plug in in the JACK audio path.

Finally I start Jamulus automatically connecting to the Directory.

Here is the script:


~~~
 amixer sset 'Mic' capture 30% cap
 amixer sset 'Mic' playback 0%
 amixer sset 'Line' playback 60% unmute
 amixer sset 'Audigy Analog/Digital Output Jack' unmute
 amixer sset 'Analog Mix' capture 100%
 amixer sset 'Analog Mix' playback 0%
 amixer sset 'Wave' 100%
 amixer sset 'Master' capture 100% cap
 amixer sset 'Master' playback 100%
 amixer sset 'Master' playback 100%
 amixer sset 'PCM' playback 100%
 amixer sset 'PCM' capture 0%
 guitarix &
 /home/corrados/llcon/Jamulus -c myJamulusServer.domain.com &
 sleep 3
 jack_disconnect system:capture_1 Jamulus:'input left'
 jack_disconnect system:capture_2 Jamulus:'input right'
 jack_connect system:capture_1 gx_head_amp:in_0
 jack_connect gx_head_amp:out_0 gx_head_fx:in_0
 jack_connect gx_head_fx:out_0 Jamulus:'input left'
 jack_connect gx_head_fx:out_1 Jamulus:'input right'
 jack_connect Jamulus:'output left' system:playback_1
 jack_connect Jamulus:'output right' system:playback_2
~~~

### Using `--ctrlmidich` for MIDI controllers

The volume fader, pan control and mute and solo buttons in the Client's mixer window strips can be controlled using a connected MIDI controller. This feature is available from version 3.7.0 on macOS, Linux, and the JACK version of Jamulus for Windows. From Jamulus 3.12.0 onwards, it is also available for the non-JACK (ASIO) Windows version.  To enable this feature, Jamulus must be launched with the `--ctrlmidich` command-line option.

When this option is used on the command line, Jamulus will prepend a channel number to each Client name, which can be used to control the channel using MIDI CC numbers. In Jamulus version 3.12.0 onwards, when connected to a server of at least version 3.5.5, your own fader will always be given channel 0, and so will appear first when unsorted or sorted by channel, whether or not "Own Fader First" is enabled.

*Tip*: With default settings, when some users leave and others join, their left-right arrangement in the GUI may cease to follow a numerical order, making it more difficult to know who each physical fader/knob on your MIDI controller corresponds to. To keep the fader strips following a numerical order, go to "View" on the top menu bar and switch to "Sort by Channel" (or type `Ctrl+E`).

When using JACK or macOS, make sure you connect your MIDI device's output port to the Jamulus MIDI in port (QjackCtl (Linux/Windows), Audio/MIDI Setup (macOS) or whatever you use for managing connections). In Linux you may need to install and launch `a2jmidid` so your device shows up in the MIDI tab in Qjackctl.  For non-JACK Windows, Jamulus will find the MIDI device(s) automatically, but see the `d` option below if more than one MIDI device is connected.

`--ctrlmidich` takes a single argument.  If you omit it, the parameter is ignored.  There are two formats for the argument:

1. The legacy definition has one or two numbers in the format:

   ```
   [MIDI channel];[offset for first fader]
   ```

   * `MIDI channel` is required or else the parameter argument is ignored and the feature is not active.  `0` means "any channel", `1`-`16` listen only to MIDI messages on the specified MIDI channel.

   * `offset for first fader` is the first MIDI CC to use to control a Jamulus Channel fader (default 70, which matches the Behringer X-Touch defaults), with all MIDI CCs after that being used; must be a number or else the long form is used.

     For example

     ```
     --ctrlmidich "0"
     ```

     would listen on all MIDI channels and use MIDI controller 70 to control Jamulus channel 0 fader and so on.  Here's another example:

     ```
     --ctrlmidich "2;50"
     ```

     This would listen on MIDI channel 2 and use MIDI controller 50 to control Jamulus channel 0 fader and so on.

2. The long form is a sequence of offsets and counts for various controllers:

   ```
   [MIDI channel];[control letter][offset](*[count])(;...)
   ```

   * `MIDI channel` is required or else the parameter argument is ignored and the feature is not active.  `0` means "any channel", `1`-`16` listen only to MIDI messages on the specified MIDI channel.

   * `control letter` defines which Jamulus Control the MIDI controller number is assigned to:

     | control letter | Jamulus Control |
     |--------:|---------|
     | `f` | Fader |
     | `p` | Pan   |
     | `s` | Solo  |
     | `m` | Mute  |

   * `offset` is the base MIDI CC number for the control.

   * `count` is the number of CC values for the control, defaulting to 1 (i.e. the number Jamulus channels that can be controlled).

   An example for a Korg nanoKONTROL2 using eight sliders starting at MIDI CC 0 to control faders and eight knobs starting at MIDI CC 16 to control pan, on any MIDI channnel, for eight Jamulus channels would be

   ```
   --ctrlmidich "0;f0*8;p16*8"
   ```

   Enhancing this to add eight Solo buttons starting at CC 32, and eight Mute buttons starting at CC 48 would give:

   ```
   --ctrlmidich "0;f0*8;p16*8;s32*8;m48*8"
   ```

   * Two additional `control letter` values are available:

     1. `o` controls Mute Myself and has a single `offset` (i.e. `count` is ignored and taken as 1).

     2. `d` is an option on Windows non-JACK Jamulus to specify a particular MIDI input device by name -- without this, all devices will be assigned to Jamulus; with it, only the specified device will be used.  For example:

        ```
        --ctrlmidich "1;f0*8;dnanoKontrol"
        ```

        would listen for CC0 through CC7 on MIDI channel 1 from a MIDI device called "nanoKontrol".  Remember to wrap the whole of the `--ctrlmidich` argument in double quotes and you will have no problems with device names containing spaces.

        In order to discover the correct device name to use, start Jamulus from the command line with `--ctrlmidich` and observe the output. Jamulus will list all discovered MIDI devices:

        ```
        C:\Users\Me>"C:\Program Files\Jamulus\Jamulus" --ctrlmidich "0"
        - MIDI controller settings: 0
        - allocated port number: 22134
        - MIDI devices found: 2
          0: nanoKONTROL2
          1: Keystation Mini 32

        C:\Users\Me>"C:\Program Files\Jamulus\Jamulus" --ctrlmidich "1;f0*8;p16*8;s32*8;m48*8;dnanoKONTROL2"
        - MIDI controller settings: 1;f0*8;p16*8;s32*8;m48*8;dnanoKONTROL2
        - allocated port number: 22134
        - MIDI devices found: 2
          0: nanoKONTROL2
          1: Keystation Mini 32 (ignored)
        ```

	Note that if only one MIDI device is connected, the `d` option is not necessary, as Jamulus will use the device automatically.

	On macOS, Linux or Windows with JACK, the `d` option is accepted if given, but ignored.

*Note*: Jamulus does not provide feedback on the on/off state of buttons, meaning that your controller must keep track and toggle LEDs (if any) to 'on' or 'off' itself, that is, buttons on your MIDI controller need to be set to "toggle" mode. This means that when pressed to 'turn on' a control, it must send a MIDI CC number with a value >=64, and to 'turn off' the control it must send the same CC number with a value <64. You can read your controller's manual to find out how to set this.

## For Server admins

### Converting a Registered Server to an Unregistered one on the fly

You can run as a Registered Server long enough for people to connect, then go "private" (Unregistered) by setting the Directory to "none" in the Server GUI. Musicians will still be connected to the Server until they disconnect. (Thanks to [David Savinkoff](https://github.com/DavidSavinkoff) for this tip!)

### Remote management of recordings

Jamulus user [vdellamea](https://github.com/vdellamea) has written a [web-based remote tool](https://github.com/vdellamea/jamulus-server-remote) for starting and stopping recordings on Linux Servers, allowing you to then download them from your browser. See also [Jamulus Jam Exporter](https://github.com/pljones/jamulus-jamexporter) by [pljones](https://github.com/pljones), which also includes a Server recording recovery script.

### Making a Server status page

With the `-m` command line argument, Server statistics can be generated to be put on a web page.

Here is an example php script using the Server status file to display the current Server status on a html page (assuming the following command line argument to be used: `-m /var/www/stat1.dat`):

~~~
<?php
function loadserverstat ( $statfilename )
{
   $datei = fopen ( $statfilename, "r" );
   while ( !feof ( $datei ) )
   {
          $buffer = fgets ( $datei, 4096 );
          echo $buffer;
   }
   fclose($datei);
}
?>
<?php loadserverstat ( "stat1.dat" ); ?>
~~~
