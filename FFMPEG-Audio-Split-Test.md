# First tests with FFMPEG

Start with [the Matlab-generated FLAC file](https://archive.org/details/72MinuteAudioFileWithTonesAt7SecondIntervals).
Rename to 2016-01-01@000000.flac

Use the following Ruby script to cut the 72 minute file in to 7-second segments.
The essentials are that the file:

- Uses SoX to calculate the full length of the file in seconds
- Starts counting at 0, and goes up 7 seconds each loop
- tells FFMPEG to chop the file with `-ss #{x} -c copy -t 7` where `x` is the current count.
	- `-ss` tells ffmpeg to seek this many seconds ahead. Note: We choose to put `-ss` *after* the input file in order to get as much precision as possible. [See the documentation](https://trac.ffmpeg.org/wiki/Seeking).
	- `-c copy` tells FFMPEG not to transcode, only to copy
	- `-t` is the duration: 7 seconds
- (optionally) adds JSON metadata, including a hash of the file
- (optionally) uses SoX to generate a sectrogram so we can easily check the output visually


---

#Test #1

Chopped the file, and compared the spectrograms: they do not line up precisely.

They even show that on some segments, there are 2 tones.

Running `sox segment.flac -n stat` shows that many segments are not actually 7 seconds long.

Example:

```
Samples read:            308736
Length (seconds):      7.000816  #Just slightly over 7 seconds
Scaled by:         2147483647.0
Maximum amplitude:     0.999969
Minimum amplitude:    -1.000000
Midline amplitude:    -0.000015
Mean    norm:          0.009172
Mean    amplitude:    -0.000021
RMS     amplitude:     0.084886
Maximum delta:         0.142395
Minimum delta:         0.000000
Mean    delta:         0.001187
RMS     delta:         0.011008
Rough   frequency:          910
Volume adjustment:        1.000
```

Out of 258 segments, 250 had a length of 7.000816 seconds, and 8 had a length of 6.974694 seconds.
Interestingly, the first segment and the last segment were both 7.000816 seconds

Example of a shorter segment:

```
Samples read:            307584
Length (seconds):      6.974694
Scaled by:         2147483647.0
Maximum amplitude:     0.999969
Minimum amplitude:    -1.000000
Midline amplitude:    -0.000015
Mean    norm:          0.006813
Mean    amplitude:    -0.000031
RMS     amplitude:     0.073146
Maximum delta:         0.199188
Minimum delta:         0.000000
Mean    delta:         0.001358
RMS     delta:         0.014577
Rough   frequency:         1398
Volume adjustment:        1.000
```


#Test #2

Suspecting the FFMPEG's FLAC decoder may be the problem, I first converted from FLAC to WAV via SoX (`sox 2016-01-01@000000.flac 2016-01-01@000000.wav`) and generated a spectrogram of the WAV to do a quick visual inspection.
After being satisfied with the inspection, the split test was run again.

Same results: spectragrams do not line up precisely, and in some segments there are 2 tones.

Running `sox segment.flac -n stat` shows that many segments are not actually 7 seconds long, yet we find that the splits are not the same as with FLAC.

Example:

```
Samples read:            309248
Length (seconds):      7.012426
Scaled by:         2147483647.0
Maximum amplitude:     0.999969
Minimum amplitude:    -1.000000
Midline amplitude:    -0.000015
Mean    norm:          0.010204
Mean    amplitude:     0.000010
RMS     amplitude:     0.089518
Maximum delta:         0.196350
Minimum delta:         0.000000
Mean    delta:         0.002004
RMS     delta:         0.017577
Rough   frequency:         1378
Volume adjustment:        1.000
```

Out of 258 segments, 189 had a length of 7.012426 seconds, and 69 had a length of 6.974694 seconds.
As before, the first segment is the longer length, but this time the last segment was 6.965986


An example of a shorter file that's not the end file:

```
Samples read:            307200
Length (seconds):      6.965986
Scaled by:         2147483647.0
Maximum amplitude:     0.999969
Minimum amplitude:    -1.000000
Midline amplitude:    -0.000015
Mean    norm:          0.006030
Mean    amplitude:    -0.000026
RMS     amplitude:     0.068827
Maximum delta:         0.190674
Minimum delta:         0.000000
Mean    delta:         0.001057
RMS     delta:         0.012414
Rough   frequency:         1265
Volume adjustment:        1.000
```


# End result

As of 2016-01, FFMPEG does not precisely split audio, and therefore cannot be used for tasks that require precision.
