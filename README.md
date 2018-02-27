# MWF toolbox for EEG artifact removal

## License

See the [LICENSE](LICENSE.md) file for license rights and limitations. 
By downloading and/or installing this software and associated files on your computing system you agree to use the software under the terms and condition as specified in the License agreement.

## Using the MWF toolbox

### About

This MATLAB toolbox implements an algorithm based on the Multi-channel Wiener Filter (MWF) 
for processing multi-channel EEG as published in [1]. The algorithm removes any type of 
artifact marked a-priori by the user from the EEG in order to enhance signal quality for 
further processing.

Developed and tested in MATLAB R2015a. Required toolboxes:
 - EEGLAB (only required for manual marking of artifacts). [EEGLAB website](https://sccn.ucsd.edu/eeglab/index.php).  
 (Make sure EEGLAB is added to the MATLAB path: you can check this by typing "eeglab" in the command window)

### Documentation

All functions are documented properly in their respective m-files. Additional documentation 
and examples can be found in the [doc](doc/) folder, which contains a 
[manual](doc/mwf_manual.pdf) in pdf format and a [MWF demo file](doc/mwf_demo.m) to illustrate 
the usage of the various functions. A quick start guide is provided in the next section.
 
### Quick start guide
 
All functions needed to perform MWF-based EEG artifact removal are in the mwf folder.
Before starting, make sure that this folder is added to the MATLAB path.
The MWF first requires examples of EEG with and EEG without artifacts. Based on this
segmentation of the EEG data, the MWF can be computed and applied in order to remove
the artifacts. This two-step approach is fully implemented in the toolbox.

**Step 1: EEG segmentation.** In the toolbox, this step is performed by manual marking
of the data using a GUI. If you have your EEG data matrix in the the MATLAB workspace
(channels x samples), you can obtain the artifact mask by calling

     mask = mwf_getmask(EEG, samplerate);
 
This pops up the GUI in which artifacts can be marked by clicking and dragging over
them. When done, clicking the 'Save Marks' button will close the GUI and the function
returns a binary (1 x samples) mask. In this mask, ones correspond to artifact segments, and
zeros correspond to clean data. Optionally, the mask may contain NaNs which indicate segments 
to be ignored from the MWF computation (i.e. they belong neither to the artifact nor the clean 
segments). Section 4.1.4. of the [manual](doc/mwf_manual.pdf) contains extra 
tips on annotating artifacts.

**Important note:** all segments that are not marked and come *before* the lasted marked 
segment will be treated as artifact-free samples in the training of the filter. The samples 
that come after the last marked segment are not used in the filter design. In other words: 
the filter design assumes that *all* artifacts before the last marked artifact are marked. 

Because samples after the lasted marked segment are ignored, it is not necessary to go through 
the entire signal to mark all the artifacts. It is sufficient to annotate only the first few 
seconds or minutes of the signal. However, the more artifacts are marked, the better the filter 
design will be. Additionally, there should be enough clean (unmarked) segments before the last 
marked artifact for a good filter design.

The artifact detection step is not inherently a part of the MWF algorithm: if you prefer,
you can also use a different method for acquiring the artifact mask (e.g. an automatic method,
for example based on thresholding,. . . ). The mask needs to consist of ones, zeros and NaNs, 
and must have the same length as the EEG data.
 
**Step 2: MWF artifact removal** is performed by calling the mwf process function. It
requires the EEG data, the mask indicating which segments are artifacts, and optionally a
delay parameter:
 
     clean_EEG = mwf_process(EEG, mask, delay);

This will return the artifact-free EEG in the clean EEG variable. Using the optional delay
parameter includes temporal information into the filter, leading to better artifact removal but
may increase processing time. If omitted, the default value is zero. See [1] for more details.


 ## References
 
 [1] B. Somers, T. Francart, A. Bertrand (2018). A generic EEG artifact removal algorithm based on the multi-channel Wiener filter. 
 Journal of Neural Engineering, 2018
