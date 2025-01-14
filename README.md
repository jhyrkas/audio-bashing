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
