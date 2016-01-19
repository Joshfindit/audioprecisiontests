# First tests with SoX

First, some sanity checks: How are we supposed to be processing through SoX in order to get the best result?

We need to use the `trim` option. SoX supports ms accuracy as well as supporting two different measurement formats:

- `trim 2520 28` meaning trim 28 seconds, starting from 2520 seconds in
- `trim 2520 =2548` meaning trim from 2520 seconds to 2548 seconds
- I attempted to trick SoX in to being more precise with `trim 2520.0000 =2548.0000`, but it turns out it wasn't neccessary. The MD5 hash of `trim 2520.0000 =2548.0000` and `trim 2520 =2548` were identical.

Following the same procotol as the FFMPEG tests, we start with [the Matlab-generated FLAC file](https://archive.org/details/72MinuteAudioFileWithTonesAt7SecondIntervals) and rename to `2016-01-01@000000.flac`

Use the Ruby script from before to cut the 72 minute file in to 7-second segments, only changing from `ffmpeg` to `sox`.
The essentials are that the file:

- Uses SoX to calculate the full length of the file in seconds
- Starts counting at 0, and goes up 7 seconds each loop
- tells sox to `trim` the file with `trim #{x} =#{x + 7}"` where `x` is the current count.
	- `trim` usage is described in Switching to SoX
- Saves the segment with the name `YYYY-MM-DD@HHMMSS.flac` where the time is determined by `2016-01-01@000000.flac` + `#{x}`
- (optionally) saves JSON metadata, including a hash of the file
- (optionally) uses SoX to generate a sectrogram so we can easily check the output visually

---

# SoX Test #1

When processing FLAC with the Ruby script, we find that all the resulting segments are 7.000000 seconds long.
Visual inspection of the spectrograms shows that the tone starts at the beginning of the segment, and there is no 'leakage' at the end of the segment. Each file has only one tone.

Example:

```
Samples read:            308700
Length (seconds):      7.000000
Scaled by:         2147483647.0
Maximum amplitude:     0.999969
Minimum amplitude:    -1.000000
Midline amplitude:    -0.000015
Mean    norm:          0.009092
Mean    amplitude:    -0.000000
RMS     amplitude:     0.084506
Maximum delta:         0.125336
Minimum delta:         0.000000
Mean    delta:         0.001140
RMS     delta:         0.010591
Rough   frequency:          879
Volume adjustment:        1.000
```


# SoX Test #2

Convert the FLAC to WAV using SoX, then split it in to segments with SoX

As expected, we find that again each segment is 7.000000 seconds long.
Visual inspection of the spectrograms, again, show that the tone starts at the beginning of each segment, and each file has only one tone.
