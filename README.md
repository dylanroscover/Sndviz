# Sndviz
A simple yet modular audio visualisation toolkit for TouchDesigner.

![Sndviz Tox Example Animation](/img/SndvizPreview.gif)

## What it does
Sndviz converts an audio CHOP stream to various TOP modules. Each module consists of discreet data in RGBA channels: high values in red, mids in green, lows in blue, and auxilary data in alpha. These modules can be used for 3d geometry instancing, 2d texturing and other real time audio visualisation use cases where numerous tox files reference the same audio stream.

## Setup
1. Drop the `Sndviz.tox` file into your TouchDesigner project. 
1. On the `Sndviz` base component parameters panel, open the `Audio` tab. Inside the `Input` section, select your preferred `Audio Input Device`. 
    * I recommend [Cable](https://www.vb-audio.com/Cable/) for Windows or [Soundflower](https://github.com/mattingalls/Soundflower) for Mac. These allow you to feed local audio (Spotify, Firefox, etc.) to TouchDesigner/Sndviz directly.
    * If you are using Cable, select `CABLE Input (VB-Audio Virtual Cable)` as the Windows sound output device, and `CABLE Output (VB-Audio Virtual Cable)` as the sound input device. 
1. To monitor sound output, select your preferred `Audio Output Device` and toggle the `Enable Audio Output` button to `On`. You can also adjust the `Output Buffer` here to compensate for video latency.

## Usage
### Single TouchDesigner Instance (Local)
1. Inside the `Sndviz` base component, open the `internalSelects` base component. 
1. Inside `internalSelects` are 16 individual Select OPs. Each OP can be copied and pasted into anywhere in your TouchDesigner project for easy referencing.
    * Due to the excellent optimisation of TouchDesigner, only Selects you use and display in your final render will be computed from Sndviz; the rest will be ignored.
1. As long as the `Sndviz` base component is not removed from your project, these Selects will be available.

### Multiple TouchDesigner Instances (Local/Network)
Sometimes it can be useful to run Sndviz on a separate machine for additional performance, compute constraints and overall flexibility.
1. Inside the `Sndviz` base component, cut (or copy) the `SndvizReceiver` base component and paste it into your destination TouchDesigner project. 
1. If the destination project is running on your local machine (localhost), you do not need to modify any parameters on the `sender` or `SndvizReceiver` base components. 
    * The Touch In/Out streams should work out of the box, so long as network ports `9988`, `8877`, and `7766` are all open and not in use. If they are in use, they can be easily changed on both the `sender` and `SndvidReceiver` base components `Network Settings` tabs, in the `Touch In Ports` and `Touch Out Ports` sections. Ensure that all ports correspond (match) between both components.
1. If the destination project is running on a remote machine on your network, select the `SndvizReceiver` base component, then the `Network Settings` tab, and change the `Computer Name / IP` parameter from `localhost` to the IP of the source machine that Sndviz is running on.
1. Inside the `externalSelects` base component (inside of `SndvizReceiver`) are 16 individual Select OPs. Each OP can be copied and pasted into anywhere in your TouchDesigner project for easy referencing.

## Changelog
### 0.2.0b
Initial public beta release.

## Documentation
### Modules
* Select OPs use the following Global OP Shorcut syntax: `op.Sndviz.op('name-of-module')`
* The following Sndviz modules are currently implemented:
    1. `avg1` -
    1. `avg2` -
    1. `beat1` - Basic beat detection that runs best with simple electronic music.
        1. `beat` channel - Each beat of the song.
        1. `counter` channel - 8-integer counter (0-7) that loops, based on the beat.
        1. `downbeat` channel - (Usually) the first or last beat of each bar (every 4 beats) of the audio.
        1. `bpm` channel - The estimated beats per minute (BPM) of the audio.
            * Special thanks to Alpha Moon Base for sharing his techniques on this detection using CHOPs.
    1. `beat2` A 2x2 matrix (TOP) indicating the beat progression through each bar of the audio.
    1. `eq`
    1. `graph1`
    1. `levels1` 
    1. `levels2`
    1. `snd` - Basic CHOP audio stream.
    1. `tex1` -
    1. `tex2` - 
    1. `tex3` -
    1. `tex4` -
    1. `vinyl1` -
    1. `waveforms1` - 

### Sndviz Custom Parameters
#### Audio
##### Input
* To change the input audio device, select a new one from the `Audio Input Device` menu parameter.
* To visualize only one channel of audio (mono) instead of both (stereo), toggle the `Stereo` parameter off.
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

## Acknowledgements

Shoutout to [Derivative](https://derivative.ca), [Elburz](https://interactiveimmersive.io), [Alpha MoonBase](https://alphamoonbase.de/), [elekktronaut](https://elekktronaut.com/) and everyone else who's helped me directly or indirectly along the way.