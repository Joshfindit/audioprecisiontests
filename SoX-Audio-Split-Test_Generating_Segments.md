# More tests with SoX: Precision of recording.

Based on the previous results, we know we can reliably split an already-created file using SoX's trim command.

For a refresher: SoX supports ms accuracy as well as supporting two different measurement formats:

- `trim 2520 28` meaning trim 28 seconds, starting from 2520 seconds in
- `trim 2520 =2548` meaning trim from 2520 seconds to 2548 seconds
- I attempted to trick SoX in to being more precise with `trim 2520.0000 =2548.0000`, but it turns out it wasn't neccessary. The MD5 hash of `trim 2520.0000 =2548.0000` and `trim 2520 =2548` were identical.

Following the same procotol as the FFMPEG tests, we start with [the Matlab-generated FLAC file](https://archive.org/details/72MinuteAudioFileWithTonesAt7SecondIntervals).
This time, however, we load it in to an Audio player (QuickTime Player, why not), then play it back while recording with SoX.

---

# Test 1: Generating segments using SoX, saving to mp3

In the previous test, a mathematecally generated file was used with tones every 7 seconds, then the file was split in to 7-second segments and each was tested for precision.

In this experiment, the mathematecally generated file is played while SoX records it with `/usr/local/bin/sox -V6 -d -c 1 ~/Sox/20160101_000000-0400.n.mp3 trim 0 7 : newfile : restart`

- `-V6` set the verbosity to level 6 (all messages including debug)
- `-d` use the default audio input (set by OSX)
- `-c 1` one channel recording (mono) 
- `trim 0 7` trim the input from the beginning to 7 seconds
- `: newfile` generate a new file (adds an incremented number to the filename)
- `: restart` after the file is generated, restart the sox command

We come up with two disturbing points from a precision perspective:

1. Each segment is not 7 seconds, but is in fact 7.000816 seconds
2. When looking at the timing of the recording of tones, **it's obvious that SoX regularly fumbles**.
	- *For example:* 25 segments will be correct, then #26 will have the tone slightly early. #26-#110 will be the same, then #111 will have the tone earlier still.
	- Suspiciously, this is not consistant. Sometimes it will be >100 segments between shifts, sometimes it will be <5.

*There are some times where this error shows in Sox: `sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.`*


# Test 2: Generating segments using SoX, saving to WAV
Suspecting that it's related to CPU usage, I switched to `wav` with the same flags.

	sox -V6 -d 20160101_000000-0400.n.wav trim 0 7 : newfile : restart

During the test, the same errors occurred.
To be fair, these errors come up most of the time while recording. Here are some examples:

```
Output File    : '20160101_000000-0400.n070.wav'
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Comment        : 'Processed by SoX'

sox DBUG trim: position 1 at 0
sox DBUG trim: position 2 at 308700
sox INFO sox: effects chain: input        44100Hz  2 channels (multi) 32 bits unknown length
sox INFO sox: effects chain: trim         44100Hz  2 channels (multi) 32 bits 00:00:07.00
sox INFO sox: effects chain: output       44100Hz  2 channels (multi) 32 bits 00:00:07.00
In:0.00% 00:08:08.20 [00:00:00.00] Out:21.5M [  ====|====  ]        Clip:145  sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
In:0.00% 00:08:10.01 [00:00:00.00] Out:21.6M [      |      ]        Clip:145
sox DBUG wav: Finished writing Wave file, 2469600 data bytes 308700 samples
```


```
Output File    : '20160101_000000-0400.n097.wav'
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Comment        : 'Processed by SoX'

sox DBUG trim: position 1 at 0
sox DBUG trim: position 2 at 308700
sox INFO sox: effects chain: input        44100Hz  2 channels (multi) 32 bits unknown length
sox INFO sox: effects chain: trim         44100Hz  2 channels (multi) 32 bits 00:00:07.00
sox INFO sox: effects chain: output       44100Hz  2 channels (multi) 32 bits 00:00:07.00
In:0.00% 00:11:16.09 [00:00:00.00] Out:29.8M [      |      ]        Clip:454  sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
In:0.00% 00:11:19.02 [00:00:00.00] Out:29.9M [      |      ] Hd:4.7 Clip:454
sox DBUG wav: Finished writing Wave file, 2469600 data bytes 308700 samples
```


```
Output File    : '20160101_000000-0400.n243.wav'
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Comment        : 'Processed by SoX'

sox DBUG trim: position 1 at 0
sox DBUG trim: position 2 at 308700
sox INFO sox: effects chain: input        44100Hz  2 channels (multi) 32 bits unknown length
sox INFO sox: effects chain: trim         44100Hz  2 channels (multi) 32 bits 00:00:07.00
sox INFO sox: effects chain: output       44100Hz  2 channels (multi) 32 bits 00:00:07.00
In:0.00% 00:28:15.57 [00:00:00.00] Out:74.8M [-=====|======] Hd:1.6 Clip:1.65ksox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
In:0.00% 00:28:20.16 [00:00:00.00] Out:75.0M [      |      ]        Clip:1.65ksox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
```

The segments are now exactly 7 seconds long, so we know there was some imprecision introduced in the conversion to mp3. Unfortunately, changing this has not solved the underlying problem.
Looking in to the timings of the segments, the same pattern occurs: at seemingly random intervals, the tone is earlier than it should be, and earlier than the segment before it.

# Test 3: Generating segments using SoX, recording from external playback, saving to WAV


For the sake of thoroughness, we took local playback out of the picture. The WAV file was loaded on an iPad Air 2 and played through Vox with no other applications running and airplane mode turned on.

Recordings are made with `sox -V6 -d 20160101_000000-0400.n.wav trim 0 7 : newfile : restart`

With playback on the other device, SoX is still not working in a precise way.

The resulting segments are 7 seconds exactly, but again show the same issue with timing.

Here's a deeper dig in to an example:

- Files 58-81 have the tone in the exact same spot. `20160101_000000-0400.n082.wav` has the tone 600ms early, and `20160101_000000-0400.n083.wav` has the tone a little more than 100ms earlier than #82. #83-86 have the tone in the same place, and `20160101_000000-0400.n087.wav` has the tone ~70ms earlier than #86.

SoX's readout for the segments are below.
Excuse the wall of text.
---
```
Output File    : '20160101_000000-0400.n081.wav'
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Comment        : 'Processed by SoX'

sox DBUG trim: position 1 at 0
sox DBUG trim: position 2 at 308700
sox INFO sox: effects chain: input        44100Hz  2 channels (multi) 32 bits unknown length
sox INFO sox: effects chain: trim         44100Hz  2 channels (multi) 32 bits 00:00:07.00
sox INFO sox: effects chain: output       44100Hz  2 channels (multi) 32 bits 00:00:07.00
In:0.00% 00:09:27.01 [00:00:00.00] Out:25.0M [      |      ]        Clip:65.1k
sox DBUG wav: Finished writing Wave file, 2469600 data bytes 308700 samples

Input File     : 'default' (coreaudio)
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Level adjust   : 1 (linear gain)

sox DBUG wav: Writing Wave file: Microsoft PCM format, 2 channels, 44100 samp/sec
sox DBUG wav:         352800 byte/sec, 8 block align, 32 bits/samp

Output File    : '20160101_000000-0400.n082.wav'
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Comment        : 'Processed by SoX'

sox DBUG trim: position 1 at 0
sox DBUG trim: position 2 at 308700
sox INFO sox: effects chain: input        44100Hz  2 channels (multi) 32 bits unknown length
sox INFO sox: effects chain: trim         44100Hz  2 channels (multi) 32 bits 00:00:07.00
sox INFO sox: effects chain: output       44100Hz  2 channels (multi) 32 bits 00:00:07.00
In:0.00% 00:09:28.17 [00:00:00.00] Out:25.1M [      |      ]        Clip:65.1ksox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
In:0.00% 00:09:28.28 [00:00:00.00] Out:25.1M [      |      ]        Clip:65.1ksox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
In:0.00% 00:09:28.56 [00:00:00.00] Out:25.1M [      |      ]        Clip:65.1ksox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
In:0.00% 00:09:29.10 [00:00:00.00] Out:25.1M [!=====|=====!] Hd:0.0 Clip:66.8ksox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
In:0.00% 00:09:29.38 [00:00:00.00] Out:25.1M [      |      ] Hd:0.0 Clip:66.8ksox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
In:0.00% 00:09:34.02 [00:00:00.00] Out:25.3M [      |      ] Hd:2.7 Clip:66.8k
sox DBUG wav: Finished writing Wave file, 2469600 data bytes 308700 samples

Input File     : 'default' (coreaudio)
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Level adjust   : 1 (linear gain)

sox DBUG wav: Writing Wave file: Microsoft PCM format, 2 channels, 44100 samp/sec
sox DBUG wav:         352800 byte/sec, 8 block align, 32 bits/samp

Output File    : '20160101_000000-0400.n083.wav'
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Comment        : 'Processed by SoX'

sox DBUG trim: position 1 at 0
sox DBUG trim: position 2 at 308700
sox INFO sox: effects chain: input        44100Hz  2 channels (multi) 32 bits unknown length
sox INFO sox: effects chain: trim         44100Hz  2 channels (multi) 32 bits 00:00:07.00
sox INFO sox: effects chain: output       44100Hz  2 channels (multi) 32 bits 00:00:07.00
In:0.00% 00:09:41.01 [00:00:00.00] Out:25.6M [      |      ]        Clip:68.5k
sox DBUG wav: Finished writing Wave file, 2469600 data bytes 308700 samples

Input File     : 'default' (coreaudio)
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Level adjust   : 1 (linear gain)

sox DBUG wav: Writing Wave file: Microsoft PCM format, 2 channels, 44100 samp/sec
sox DBUG wav:         352800 byte/sec, 8 block align, 32 bits/samp

Output File    : '20160101_000000-0400.n084.wav'
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Comment        : 'Processed by SoX'

sox DBUG trim: position 1 at 0
sox DBUG trim: position 2 at 308700
sox INFO sox: effects chain: input        44100Hz  2 channels (multi) 32 bits unknown length
sox INFO sox: effects chain: trim         44100Hz  2 channels (multi) 32 bits 00:00:07.00
sox INFO sox: effects chain: output       44100Hz  2 channels (multi) 32 bits 00:00:07.00
In:0.00% 00:09:48.02 [00:00:00.00] Out:25.9M [      |      ]        Clip:69.4k
sox DBUG wav: Finished writing Wave file, 2469600 data bytes 308700 samples

Input File     : 'default' (coreaudio)
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Level adjust   : 1 (linear gain)

sox DBUG wav: Writing Wave file: Microsoft PCM format, 2 channels, 44100 samp/sec
sox DBUG wav:         352800 byte/sec, 8 block align, 32 bits/samp

Output File    : '20160101_000000-0400.n085.wav'
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Comment        : 'Processed by SoX'

sox DBUG trim: position 1 at 0
sox DBUG trim: position 2 at 308700
sox INFO sox: effects chain: input        44100Hz  2 channels (multi) 32 bits unknown length
sox INFO sox: effects chain: trim         44100Hz  2 channels (multi) 32 bits 00:00:07.00
sox INFO sox: effects chain: output       44100Hz  2 channels (multi) 32 bits 00:00:07.00
In:0.00% 00:09:55.01 [00:00:00.00] Out:26.2M [      |      ]        Clip:71.2k
sox DBUG wav: Finished writing Wave file, 2469600 data bytes 308700 samples

Input File     : 'default' (coreaudio)
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Level adjust   : 1 (linear gain)

sox DBUG wav: Writing Wave file: Microsoft PCM format, 2 channels, 44100 samp/sec
sox DBUG wav:         352800 byte/sec, 8 block align, 32 bits/samp

Output File    : '20160101_000000-0400.n086.wav'
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Comment        : 'Processed by SoX'

sox DBUG trim: position 1 at 0
sox DBUG trim: position 2 at 308700
sox INFO sox: effects chain: input        44100Hz  2 channels (multi) 32 bits unknown length
sox INFO sox: effects chain: trim         44100Hz  2 channels (multi) 32 bits 00:00:07.00
sox INFO sox: effects chain: output       44100Hz  2 channels (multi) 32 bits 00:00:07.00
In:0.00% 00:10:02.00 [00:00:00.00] Out:26.5M [      |      ]        Clip:73.0k
sox DBUG wav: Finished writing Wave file, 2469600 data bytes 308700 samples

Input File     : 'default' (coreaudio)
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Level adjust   : 1 (linear gain)

sox DBUG wav: Writing Wave file: Microsoft PCM format, 2 channels, 44100 samp/sec
sox DBUG wav:         352800 byte/sec, 8 block align, 32 bits/samp

Output File    : '20160101_000000-0400.n087.wav'
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Comment        : 'Processed by SoX'

sox DBUG trim: position 1 at 0
sox DBUG trim: position 2 at 308700
sox INFO sox: effects chain: input        44100Hz  2 channels (multi) 32 bits unknown length
sox INFO sox: effects chain: trim         44100Hz  2 channels (multi) 32 bits 00:00:07.00
sox INFO sox: effects chain: output       44100Hz  2 channels (multi) 32 bits 00:00:07.00
In:0.00% 00:10:02.49 [00:00:00.00] Out:26.6M [      |      ]        Clip:73.0ksox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
sox WARN coreaudio: coreaudio: unhandled buffer overrun.  Data discarded.
In:0.00% 00:10:09.01 [00:00:00.00] Out:26.9M [      |      ] Hd:4.3 Clip:74.8k
sox DBUG wav: Finished writing Wave file, 2469600 data bytes 308700 samples

Input File     : 'default' (coreaudio)
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Level adjust   : 1 (linear gain)

sox DBUG wav: Writing Wave file: Microsoft PCM format, 2 channels, 44100 samp/sec
sox DBUG wav:         352800 byte/sec, 8 block align, 32 bits/samp

Output File    : '20160101_000000-0400.n088.wav'
Channels       : 2
Sample Rate    : 44100
Precision      : 32-bit
Sample Encoding: 32-bit Signed Integer PCM
Endian Type    : little
Reverse Nibbles: no
Reverse Bits   : no
Comment        : 'Processed by SoX'

sox DBUG trim: position 1 at 0
sox DBUG trim: position 2 at 308700
sox INFO sox: effects chain: input        44100Hz  2 channels (multi) 32 bits unknown length
sox INFO sox: effects chain: trim         44100Hz  2 channels (multi) 32 bits 00:00:07.00
sox INFO sox: effects chain: output       44100Hz  2 channels (multi) 32 bits 00:00:07.00
In:0.00% 00:10:16.00 [00:00:00.00] Out:27.2M [   ===|===   ]        Clip:74.8k
sox DBUG wav: Finished writing Wave file, 2469600 data bytes 308700 samples
```

The most obvious red flag file is #82, so here are the stats:

```
$ sox 20160101_000000-0400.n082.wav -n stat

Samples read:            617400
Length (seconds):      7.000000
Scaled by:         2147483647.0
Maximum amplitude:     1.000000
Minimum amplitude:    -1.000000
Midline amplitude:    -0.000000
Mean    norm:          0.011100
Mean    amplitude:     0.000007
RMS     amplitude:     0.081444
Maximum delta:         1.024212
Minimum delta:         0.000000
Mean    delta:         0.001581
RMS     delta:         0.019782
Rough   frequency:         1704
Volume adjustment:        1.000
```

For comparison, here's #85

```
$ sox 20160101_000000-0400.n085.wav -n stat

Samples read:            617400
Length (seconds):      7.000000
Scaled by:         2147483647.0
Maximum amplitude:     1.000000
Minimum amplitude:    -1.000000
Midline amplitude:    -0.000000
Mean    norm:          0.011643
Mean    amplitude:     0.000006
RMS     amplitude:     0.083079
Maximum delta:         0.992008
Minimum delta:         0.000000
Mean    delta:         0.001879
RMS     delta:         0.020434
Rough   frequency:         1726
Volume adjustment:        1.000
```
