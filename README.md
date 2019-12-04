# python-pesq

This is a fork from https://github.com/ludlows/python-pesq with minor adaptations.

PESQ (Perceptual Evaluation of Speech Quality) Wrapper for Python Users, adhering to ITU-P862.2.

This code is designed for `numpy` array specially. It has nearly `3x` speed up vs. calling C-compiled executable from shell.

## Preliminary


ITU-P862.2 is a wide-band extension of P862. It also gives a narrow-band mode which is compatible (has same result) with P862.

* MOS (Mean Opinion Score): raw MOS PESQ score, range `(-0.5,4.5)`
* LQO (Listening Quality Objective): a mapping from MOS, range `(1.02,4.56)`

The official ANSI C code is provided from [1].

If you would like to test the difference, please download from [1] and compile with,

    gcc pesqmain.c pesqdsp.c dsp.c pesqmod.c pesqio.c -o pesqmain -lm

The executable can be run with,

    # wide band
    ./pesqmain +wb +16000 reference_audio_path degenerated_audio_path 
    # adhering to ITU-P862
    ./pesqmain +16000 reference_audio_path degenerated_audio_path 

## Requirements

    gcc compiler
    numpy
    cython

## Build and Install
```bash
$ git clone https://github.com/serser/python-pesq.git
$ cd python-pesq
$ pip install .  # for python 2
$ pip3 install . # for python 3 
$ cd ..
$ rm -rf python-pesq # remove the code folder since it exists in the python package folder
```

## Install with pip
```
$ pip install https://github.com/serser/python-pesq/archive/master.zip

# or

$ pip3 install https://github.com/serser/python-pesq/archive/master.zip
```

## Example for narrow band and wide band

```python
from scipy.io import wavfile
from pypesq import pypesq

rate, ref = wavfile.read("./audio/speech.wav")
rate, deg = wavfile.read("./audio/speech_bab_0dB.wav")

lqo = pypesq(rate, ref, deg, 'wb')
print("MOS-LQO:", lqo)

# this raw_mos is the same as ITU-P862
raw_mos, lqo = pypesq(rate, ref, deg, 'nb')
print("Raw MOS:", raw_mos, "MOS-LQO:", lqo)

# convert in decimal degrees to a precision of 3 (to match the official executable)
raw_mos = float("{0:.3f}".format(raw_mos))

```

# Correctness

The correctness is verified by running samples in audio folder.

PESQ computed by this code in *wide band* mode (wb) is    `1.0832337141036987`.

PESQ computed by this code in *narrow band* mode (nb) is  `(1.9686204195022583, 1.6072081327438354)`.

# Note

Sampling rate (fs|rate) - No default. Must select either 8000Hz or 16000Hz.
 
Note there is narrow band (nb) mode only when sampling rate is 8000Hz.

The original C soure code is modified. 

# References

1. https://www.itu.int/rec/T-REC-P.862/en