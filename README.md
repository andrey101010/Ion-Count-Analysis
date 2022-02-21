{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "<a href=\"https://colab.research.google.com/github/https-deeplearning-ai/tensorflow-1-public/blob/master/C1/W4/ungraded_labs/C1_W4_Lab_2_image_generator_with_validation.ipynb\" target=\"_parent\"><img src=\"https://colab.research.google.com/assets/colab-badge.svg\" alt=\"Open In Colab\"/></a>"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "xB2cQUShkXNm"
   },

# Ion-Count-Analysis

Sensitivity is an important parameter in mass spectrometry since is tells the user or scientist the response of an analytical system to an analyte under 
well-defined conditions. Sensitivity is characterized as the slope of a curve when an analyte amount is plotted against signal intensity [34]. 
Ionization efficacy is a critical parameter for sensitivity. However, the ion transmission of the mass analyzer, as well as the ion extraction 
from the ion source and the detectors (often microchannel plate, MCP) response to the incoming ions, are also important factors. To calculate the 
ionization efficacy, the other parameters have to be known or approximated. For instance, the response efficacy of a MCP for ions around 1 kDa is 
experimentally determined to be close to 100 % [122]. This assumption is also implemented in our upcoming consideration. Further, the time-of-flight 
mass analyzer has a high ion transmission close to 100 %, and for simplification purposes, we round up [122]. The ionization efficacy and the ion 
extraction is challenging to treat differentially, and therefore we combine the two-parameter into a single term: Ion collection yield. 
The latter is the ratio between the ionized particles arriving at the detector divided by the total number of particles interacting with the 
laser beam. As the laser focal spot size and the sample thickness can be determined accurately, the total volume can be calculated. 
Additional knowledge about the sample concentration gives the total number of particles in the excited volume. Finally, the ionized particles 
arriving at the detector produce a current and ultimately voltage at the MCP. The more particles arrive at the detector, the more voltage is produced. 
Some commercial instruments have the possibility to determine the number of ions producing a signal [45]. 
Another approach is to look at the signal intensity and the noise. Multiple parameters are contributing to the overall mass spectrum. 
The count statistic in TOF is dominated by Gaussian noise for the background and by Poisson noise for the analyte signal [123]. 
Both statistics overlap in a regular mass spectrum, but once the peaks are cleaned from the Gaussian noise, 
the Poisson statistic can provide a good estimate of the ions producing the signal. 

![screenshot](https://github.com/andrey101010/Femtosecond_Mass_Spectrometry/blob/2a7df919bfff97cf6a2b29e0024f1dec07f905f8/2019-09-15%20ion%20count%20analysis%20raw%20data,%20baseline%20correction%20and%20filtered%20data.png)
*Data processing of bradykinin signal. A) A data set of several unprocessed mass spectra. Dots of the same color indicate the same mass spectrum. Dots of different colors are arranged in vertical stacks, indicating a bin unit. B) Baseline correction is performed. C) Additional filtering is applied to filter outliers. D) Each mass spectrums integral is divided by the mean mass spectrum integral. The ratio is then used to weigh the bradykinin signal for greater comparability.* 

In our approach we assume the fact that a low number of ions is causing high voltage fluctuation and vice versa. 
These fluctuation can be expressed in sample standard deviations ultimately revealing the relationship between voltage intensity triggered by a single ion.

We first determined the Gaussian noise, by looking at the position where ions were not expected to arrive. 
In our case the recorded time for a single mass spectrum was 40 μs and in the last 5 μs the arrival of ions was never observed. 
This time window was used to determine the sample standard deviation (SSD, s_(system noise)^2). 
System noise consists of several factors including electrical and thermal effects, 
misalignment of equivalent bins as well as charging and discharging effects causing non-zero baseline [124]. 
For simplification reason we accounted only electrical effects in our calculation.
Next, we choose up to 40 single shot mass spectra with bradykinin signal, which have a comparable arrival time but not necessary the same intensity. 
Jittering of the bradykinin signal in time produces a higher variation therefore we investigated bradykinin arrival times within a 2 ns window. 
The peak of the bradykinin signal represented the arrival time.
As the digitizer records a voltage each nanosecond binning was performed to reduce the noise for each data point. 
The size of a bin accumulated 8 ns of data in the mass spectrum. From the above mentioned data set an average mass spectrum was created and also binned. 
The integral of the average mass spectrum served to weight the single shot bradykinin signal. 
This approach takes into account different signal intensities and their impact on the upcoming χ2 statistics ultimately allowing to investigate 
signal with low and high intensity at the same time. As we deal with categorical data PYTHON module lmfit (Version 1.0.2) was used to determine the reduced χ2. 
Additionally, reduced χ2 served as correction estimate a posteriori to obtain the SSD (s_(bradykinin signal noise)^2). 
Now the system SSD was subtracted from the bradykinin signal noise SSD leaving SSD (s_(bradykinin ions)^2) of bradykinin ions
