audio-bashing
=========
This repo contains code for the frequency bashing and amplitude whacking algorithms for roughness control in mixes of audio files.
For a description of the algorithms, please read the [CMMR 2023 paper](https://doi.org/10.5281/zenodo.10113480) and/or watch the 
[video presentation](https://www.youtube.com/watch?v=5FW7iM-qySU&t=8044s). Additional details can be found in the supplemental
material [on my website](https://jeremyhyrkas.com/CMMR2023).

## Installing
This code uses python3 and a number of standard libraries. The most uncommon library used is [smstools](https://github.com/MTG/sms-tools),
which is used to sinusoidally model input signals. The sine tracks discovered in analysis are used to inform the bashing/whacking algorithms,
but no sounds are resynthesized in the process.

The easiest way to install all necessary libraries is using pip:

> pip3 install -r requirements.txt

## Generating examples from the paper
Once the requirements are installed, you can generate the examples used in the paper. This is also a good test to make sure that everything
is working correctly. If you encounter errors, the requirements may not have been installed correctly. You can also reach out to me with issues.
This code has been tested on macOS only.

To generate the examples from the paper, you will need to generate the necessary files and then run the generate_paper_examples.py script.
On a Unix-like OS, starting from the root direct run the following commands:

> cd paper_examples/audio_files
> python3 generate_wavs.py
> cd ..
> python3 generate_paper_examples.py

This should create several .wav files in paper_examples/audio_files and several .png and .pdf files in the paper_examples directory.

* equations.pdf/.png: Figure 1 in the paper
* freq_bashing.pdf/.png: Figure 2 in the paper
* amp_whacking.pdf/.png: Figure 3 in the paper
* spect_difference.pdf/.png: Figure 4 in the paper (minus some additional annotations)
* spect_difference2.pdf/.png: Figure 5 in the paper

Some additional figures are generated that were not used in the paper.

* cb_models.pdf/.png: This figure compares four measurements of critical bandwidth and how they effect dissonance curves. In each plot, the x-axis represents the frequency of a second sinusoid (the first sinusoid is always centered at the left-most frequency in the graph). The top row shows four models of critical bandwidth; the bottom row shows dissonance curves, with the dotted vertical lines indicating the frequency of maximum dissonance based on the various models.
* sethares_axes.pdf/.png: This figures illustrates that the frequency ratio of maximum dissonance changes across the frequency band (and is therefore not related to musical intervals).

## Running the algorithms
The two main scripts in this codebase are audio_basher.py and audio_whacker.py. The first controls roughness by changing the frequencies of certain partials in audio files, while
the latter changes their amplitudes. General use of the software works as follows:

> python3 audio_[basher/whacker].py [list of options] [list of input audio files]

The input files will be analyzed and processed to either reduce or increase the roughness of the audio mix, depending on parameter settings.
Audio files will be written in the .wav format:

Audio basher:

* bashed.wav: contains the audio files mixed together after changing the frequency of some sound partials.
* filtered.wav: contains the audio files mixed together with clashing harmonics removed. This is sometimes useful or preferable to bashed.wav. This file will always be more consonant than the vanilla mix, whereas bashed.wav can be made to be more dissonant with user parameterization.
* diff.wav: contains only the sonic difference between the vanilla mix and bashed.wav. This can be useful if you want to hear what was changed.

Audio whacker:

* whacked.wav: contains the audio files mixed together after changing the amplitude of some sound partials.

In most cases, bashed.wav or whacked.wav are the files you are interested in. The others exist as alternative options, sanity checking, or used to generate figures in the paper.

### Parameters
The following parameters can be set to change the behavior of the algorithms:

* -nsines=N: sets the number of maximum sine tracks at any given time used to model each audio file. The default is 10 (note: this number can be low, as only prominent partials should be tracked and there is no sinusoidal resynthesis)
* -bw_percent_low=f: sets the lower bound on sine differences to be analyzed. Range is [0-1], as a fraction of critical bandwidth. The default is 0.1 (i.e. partials closer than 10% of a critical band apart will not be adjusted for roughness).
* -bw_percent_high=f: sets the upper bound on sine differences to be analyzed. Range is [0-1], as a fraction of critical bandwidth. The default is 0.3 (i.e. partials farther than 33% of a critical band apart will not be adjusted for roughness).
* --consonance=T/F: sets whether the audio files should be adjusted to make the mix more consonant or dissonant. Default is True (i.e. bashed.wav or whacked.wav will be more consonant than the vanilla mix)
* --normalize=T/F: sets whether the audio files should be normalized individually. Default is False. Setting this to True will destroy any level balancing present in the original files.
* -roughness_thresh=f: sets the minimum threshold that a pair of sinusoids should generate to be considered for adjustment. The default is 0.0001. Reasonable values for this parameter change if using different roughness measurements. Setting this parameter to 0.0 forces all pairs of partials to be considered.
* --hard_bash=T/F: audio basher only. If this parameter is true, partials that contribute to roughness will be adjusted so that new parameters are exactly the same distance apart in Hz. This distance is set by the -delta parameter (see below). This can be useful in creating a tremolo-like effect. The default value is False.
* -delta=f: audio basher only. If --hard-bash is enabled (see above), partials that contribute to roughness will be adjusted to be exactly f Hz apart.
* -whack_percent=f: audio whacker only. The amplitude whacker will determine the optimal amplitudes for adjusted partials and move them to a fractional percent of the optimum values. The parameter ranges from [0-1], and the default is 1 (i.e. partials are always adjusted 100%).
