# msx-tape-read
MSX tape wav file to text decoder

# MSX tape wav file to text decoder
 Copyright (C) 2021 Weber Kai.
 Email: weberkai@yahoo.com.br

 This software was created from the software below

# Tape wav file to text decoder
 Copyright (C) 2006 Martin Ward.
 Email: martin@gkc.org.uk

 This program is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 2 of the License, or (at your option) any later version.

 This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License for more details.

 You should have received a copy of the GNU General Public License along with this program; if not, write to the Free Software Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA 02111-1307, USA.

 Read a wav file recording of a UK101 or similar tape in Kansas City format.
 A '0' bit is represented as one cycle of a 1200 Hz sine wave, and a '1' bit as two cycles of 2400 Hz. This gives a data rate of 1200 bauds.
 Carrier wave is 1 bits (1200Hz)
 Each frame starts with one start bit (a '0') followed by eight data bits (least significat bit first) followed by two stop bits ('1's)
 So each frame is 11 bits, for a data rate of 109 bytes per second.

 MSX tape wav file to text decoder versions
 Version 1.00: First Public Release

 Tape wav file to text decoder versions
 Version 1.00: First Public Release
 Version 1.10: Various fixes
 Version 1.20: Improvements to handle speed variations

 A typical wav file is 44100Hz sampling rate, stereo, 16 bit PCM (i.e. standard CD format).
 One cycle at 2400Hz is 18.375 samples
 One cycle at 1200 Hz is 36.75 samples
 One bit is 147 samples
 One frame is 147*11 = 1617 samples
 With a 128 sample window, there can fit 6.966 waves of 2400Hz signal and 3.483 waves of 1200Hz signal
 For 1200 baud, to fit one wave in a 64 bit window we need to resample to 76800Hz
 BBC Micro cassette tape appears to work with: tape-read hi=2400 lo=1200 baud=1200 resample=64 frame=8N1 file.wav

 For Atari 400/800 tapes we need: tape-read hi=5327 lo=3995 baud=600 frame=8N1 file.wav
 See: http://www.atariarchives.org/dere/chaptC.php

 CUTS tapes can be hard to decode because the zero bit is only half a cycle of the low frequency. The script has to analyse a two bit wide window.
 Using the welch window function can help since it gives more weighting to the centre of the window.
 (1) Save the CUTS tapes at low volume (to avoid clipping) and a high bitrate
 (2) Use a wav editer to work out the exact bit rate (1 cycle of high frequency)
 (3) Resample to 64 or 128 samples per bit and use 8 or 16 steps per bit.
 (4) Use a welch window. For example: tape-read hi=1120 lo=560 baud=1120 resample=128 steps=32 window=welch file.wav

 The challenge was to load the data from an old brazilian radio program which aired some MSX code: Rádio USP - Clip Informática - Episode: 102
 The data starts at 25m00s from this video: https://www.youtube.com/watch?v=QSzMP64DPnI

 First download the mp4 video from YT, then extract the aac audio with:
    ffmpeg -i MSX.mp4 -vn -acodec copy MSX.aac
 Then convert aac to wav:
    ffmpeg -i MSX.aac MSX.wav
 In Audacity cut only the MSX tape data
 Finally use this code to read the data
    perl msx-tape-read baud=1250 hi=2500 lo=1250 window=welch MSX.wav
 
 You should have 6 files now each one corresponding to the data inside each tape block.
