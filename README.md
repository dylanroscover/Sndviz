# Sndviz
A simple, modular audio visualisation toolkit for TouchDesigner 2020.22080 (Windows 10 x64)

![Sndviz Tox Example Animation](/img/SndvizPreview.gif)

## What it does
Sndviz analyzes an audio device, file or CHOP input into various TOP textures, or 'modules'. Each module consists of 4 discreet data channels (RGBA): high values in red, mids in green, lows in blue, and auxilary data in alpha. These modules can be used for 3d geometry instancing, 2d texturing and other real time audio visualisation use cases where numerous tox files reference the same audio stream.

## Setup
1. Drop the [Sndviz.tox](/Sndviz.tox) file into your TouchDesigner project. 
1. On the `Sndviz` base component parameters panel, open the `Audio` page. Inside the `Input` section, select your preferred `Audio Input Device`, `Audio File`, or roll your own input with an `In CHOP`. 
    * I recommend [Cable](https://www.vb-audio.com/Cable/) for Windows or [Soundflower](https://github.com/mattingalls/Soundflower) for Mac. These allow you to feed local audio (Spotify, Firefox, etc.) to TouchDesigner/Sndviz directly.
    * If you are using Cable, select `CABLE Input (VB-Audio Virtual Cable)` as the Windows sound output device, and `CABLE Output (VB-Audio Virtual Cable)` as the sound input device. 
1. Once your preferred audio source is selected and playing, sound data should begin visualising across all Sndviz modules. If they remain black, verify your audio source is correctly configured, as well as the Sndviz `Audio` parameters.
1. To monitor sound output, select your preferred `Audio Output Device` and toggle the `Enable Audio Output` button to `On`. You can also adjust the `Output Buffer` here to compensate for video latency.

## Usage
### Single TouchDesigner Instance (Local)
1. Inside the `Sndviz` base component, open the `internalSelects` base component. 
1. Inside the `parSelects` base component are Select TOPs referencing a `SndvizReferences` Parameter DAT. This network can be copied and pasted anywhere in your TouchDesigner project for easy referencing. (Make sure to include the Parameter DAT along with the selects or they will lose their reference.)
1. Inside the `globalSelects` base component are Select TOPs directly referencing Sndviz. Each of these can be copied and pasted anywhere in your TouchDesigner project for easy referencing.
    * Due to the optimised nature of TouchDesigner, only Selects you use and display in your final render will be computed from Sndviz; the rest will be ignored.
    * As long as the `Sndviz` base component is included your project, all selects will be available.

### Multiple TouchDesigner Instances (Local/Network)
Sometimes it can be useful to run Sndviz on a separate machine for additional performance, compute constraints and overall flexibility.
1. Inside the `Sndviz` base component, cut (or copy) the `SndvizReceiver` base component and paste it into your destination TouchDesigner project. 
1. If the destination project is running on your local machine (localhost), you do not need to modify any parameters on the `sender` or `SndvizReceiver` base components. 
    * The Touch In/Out streams should work out of the box, so long as network ports `9988`, `8877`, and `7766` are all open and not in use. If they are in use, they can be easily changed on both the `sender` and `SndvidReceiver` base components `Network Settings` pages, in the `Touch In Ports` and `Touch Out Ports` sections. Ensure that all ports correspond (match) between both components.
1. If the destination project is running on a remote machine on your network, select the `SndvizReceiver` base component, then the `Network Settings` page, and change the `Computer Name / IP` parameter from `localhost` to the IP of the source machine that Sndviz is running on.
1. Inside the `globalReceiverSelects` base component (inside of `SndvizReceiver`) are 16 individual Select OPs. Each OP can be copied and pasted into anywhere in your TouchDesigner project for easy referencing.

## Demos
Demo toxes are included in the [/demo/](/demo/) folder, demonstrating how Sndviz modules can be integrated into your project.

## Changelog

### 0.3.2b
* Added Base Outs to `Sndviz` base component
* Fixed mono bug when using an In CHOP as an Audio Source
* Beat detection performance improvements and bug fixes
* Added parameter toggles for Touch Out CHOPs (Stream/Beat)
* Added `Speed` and `Reset` parameters for several modules

### 0.3.1b
* Minor performance improvements in `audioSrc` base component
* Updated Select TOP references (Parameter selects, Global selects and Global Receiver selects options)
* Added initial demo scene [sunSpotsDemo.tox](/demo/sunSpotsDemo.tox)

### 0.3.0b
* Added `Audio File In` and `CHOP In` parameters
* Updated binding setup
* Added `Modules` page with path parameters and example Parameter DAT / Select TOP in `Sndviz/internalSelects`
* Optimised execute DATs and several modules
* Added automatic audio normalisation (and automatic line noise reduction when silence is detected)
* Added Touch Out toggle parameter
* Minor bug fixes

### 0.2.0b
Initial public beta release.

## Documentation
### Modules
#### Selection
* Select OPs locally use the following Global OP Shorcut syntax: `op.Sndviz.op('name-of-module')`.
* Select OPs between different Touch instances using: `op.SndvizReceiver.op('name-of-module')`.
* OP shortcuts can also be found on the `Modules` page of the Sndviz base component.

The following Sndviz modules are currently implemented (all TOPs are in RGBA format):
1. `avg1` - A 1x1 pixel of averages for high (red), mid (green) and low (blue) ranges. Alpha channel is an average of all ranges.
1. `avg2` - A 4x4 matrix of averages for high (red), mid (green) and low (blue) values. Alpha channel is an average of all ranges.
1. `bars1` - A 1D feedback texture of detected beats (RGBA). Alpha channel is float, RGB are boolean values.
1. `beat1` - Basic beat detection that runs best with simple electronic music.
    1. `beat` channel - Each beat of the song.
    1. `counter` channel - 8-integer counter (0-7) that loops, based on the beat.
    1. `downbeat` channel - (Usually) the first or last beat of each bar (every 4 beats) of the audio.
    1. `bpm` channel - The estimated beats per minute (BPM) of the audio.
        * Special thanks to Alpha MoonBase for sharing his techniques on this detection using CHOPs.
1. `beat2` A 2x2 matrix (TOP) indicating the beat progression through each bar of the audio.
1. `eq1` - A 2D graphic equilizer showing audio levels for low (blue), mid (green) and high (red) ranges, from left to right. Alpha channel included.
1. `graph1` - A 2D feedback texture of audio levels, with the most current sample of music on the far right column, and the oldest on the far left.
1. `levels1` - A 1D texture of current audio levels.
1. `levels2` - A 2D texture of all current levels (RGB) and a feedback texture of overall levels (Alpha).
1. `snd` - Basic CHOP audio stream (pass through).
1. `tex1` - A 2D psuedo-generative texture that displaces audio levels through reaction diffusion.
1. `tex2` - A 2D psuedo-generative texture that displaces audio levels through visual noise. It's like, so meta, bro.
1. `tex3` - A low res (1/10th resolution of the Tile Resolution)
1. `tex4` - A 2D texture of all current levels, sampled with visual feedback to add a more dynamic effect.
1. `vinyl1` - A spinning disc that is 'etched' with sound levels, starting from twelve o'clock (current sample strip) and proceeding clockwise.
1. `waveforms1` - A waveform visual with red (left channel), green (right channel), blue (red minus green) and alpha (red feedback) channels.

### Sndviz Custom Parameters
#### Audio
##### Input
* Three `Audio Source` types are supported in Sndviz: `Audio Device In` (Stream), `In CHOP` (Stream) and `Audio File` (Cued playback).
    * To use an audio device (`Audio Device In` setting), select this type from the `Audio Source` menu and choose the preferred device (unless `Default` is your preference) from the `Audio Device In` menu parameter.
    * To use the `In CHOP` setting, select this type from the `Audio Source` menu and connect a CHOP to the `Sndviz` base component input connection.
    * To use an audio file (`Audio File` setting), select this type from the `Audio Source` menu and specify an audio file through the `Audio File` file parameter. Note that if no file is specified or there is an error loading an audio file, Sndviz will temporarily switch to the generic Audio file that comes bundled with Touch.
    * To play an audio file from the very beginning, pulse the `Cue File` parameter.
    * To pause / resume playing an audio file, switch the `Play/Pause File` toggle.
    * To loop an audio file, enable the `Loop File` toggle.
* To visualize only one channel of audio (mono) instead of both (stereo), toggle the `Stereo` parameter off.
* To have audio levels automatically normalized (well, psuedo-normalized... they are scaled to fit a range of -1 to 1, rather than 0 to 1), enable the `Normalize` toggle. This prevents audio levels from becoming too quiet or too loud during performances.
* To increase the intensity (gain) of visualized colours, increase the `Input Gain (db)` parameter.
* To compensate for video latency, adjust the `Input Buffer (sec)` parameter.
* To increase the quality and resolution of the vizualisations, increase the `Sampling Quality (FFT)` parameter. Keep in mind that this comes at a cost with CPU compute times.

##### Output
* To enable audio output on the local machine, turn on the `Enable Audio Output` parameter.
* To change the output audio device, select a new one from the `Audio Output Device` menu parameter.
* To raise or lower the playback output audio device volume, adjust the `Playthru Volume` parameter.
* To additionally compensate for video latency, adjust the `Output Buffer (sec)` parameter.

#### Video
* To additionally increase (or decrease) the resolution of the visualisations, adjust the `Global Res Multiplier` parameter. 
    * The `Tile Resolution` parameter (read-only) indicates the resolution of each square tile of the full Sndviz texture (4 x 3 tiles in total).
* To correct the ratio of red, green and blue channels visualised, adjust the `RGB Multiplier` parameter.
* Jump Cuts on `tex1` and `tex2` (triggered on each downbeat) may be disabled via the `Jump Cut` toggle parameter.
* By default Sndviz is a `32-bit` float component, for maximum accuracy and quality. (It also transmits 32-bit float values over the network to `SndvizReceiver`). Sndviz can be set to `8-bit` or `16-bit` to improve performance via the `Pixel Format` parameter.
* To adjust the quickness or slowness of audio visuals with feedback, use the `Speed` parameter. Default is 1. This mostly uses a pixel per frame calculation.
* To reset audio visuals with feedback, pulse the `Reset` parameter.

#### Modules
* To enable Touch Out functionality for network setups, enable any of the desired `Enable Touch Out` toggles.
* Every output module in Sndviz is listed on the `Modules` page. These read-only values may be copied and pasted for use in other components.

## Acknowledgements

Shoutout to [Derivative](https://derivative.ca), [Elburz](https://interactiveimmersive.io), [Tim Franklin](http://timsfranklin.com/), [Alpha MoonBase](https://alphamoonbase.de/), [elekktronaut](https://elekktronaut.com/) and everyone else who's helped me directly or indirectly along the way.