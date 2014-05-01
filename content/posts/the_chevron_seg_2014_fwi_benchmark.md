---
title: First thoughts on the Chevron/SEG 2014 FWI benchmark dataset
description: Some thoughts relating to a new benchmark synthetic seismic dataset from Chevron and the SEG, aimed at testing Full-Waveform Inversion.
date: "2014-05-01"
categories:
    - "FWI"
    - "Geophysics"
    - "Seismic"
    - "Tomography"
    - "Python"
    - "Science"
---

It's May of 2014, and there's a new synthetic dataset from [Chevron][] and the [SEG][] (Society of Exploration Geophysicists). This is a new entry in a series of synthetic seismic datasets designed to test the efficacy of FWI (Full-Waveform Inversion), and other advanced imaging techniques in the exploration seismic realm. I'm not going to go into too much detail about the dataset here, although you can get the material [directly from Chevron and the SEG][Dataset] if you're interested in trying the challenge. I've also prepared an [introductory iPython notebook][GHUB] that can be modified and reworked (under its license), or [viewed online][VIEW]. Instead, I'm going to talk a bit about the reasons that this sort of dataset exists, and what can be learned.

## [Seismic] Full-Waveform Inversion

Full-Waveform Inversion (FWI) is a series of methods used in exploration seismology to construct earth models from observed seismic waveforms. The models are like images of the earth, and typically take the form of gridded velocities; density, bulk modulus, shear modulus, models of anisotropy and others are also important related parameters. [Virieux and Operto (2009)](#Virieux:2009) discuss some of the details of these methods, and [Vigh and Starr (2008)](#Vigh:2008) outline some of the specifics relating to the choice of technique. The various FWI methods try to construct models of the earth that match measurements, and these measurements take the form of waveforms recorded by geophones (which measure particle velocity), hydrophones (which measure pressure) or other more esoteric instruments. FWI is the business of *inverting* the systems (matrices) of physical laws (partial differential equations) that relate the model (what we want) to the data (what we measure)---in other words, the business of making a model of the earth from the waves we record.

[Herrmann et al. (2013)](#Herrmann:2013) and [Smithyman et al. (2014)](#Smithyman:2014) show results from some projects that I was recently involved in.

## Motivation for synthetic (blind) tests

In the real world, full-waveform inversion is increasingly being used to image the Earth. FWI is among the most advanced methods currently available, but it is also prone to systematic errors that are very difficult to detect. One reason for this is the concept of *nonuniqueness*, i.e., the fact that the data we measure can be explained by more than one model. Only one of these models is representative of the true Earth, but usually we can't tell which it is. We are also missing measurements, some of them are incorrect or conflicting, our simulations aren't accurate enough and we have limited resources. So, in practice we do the best we can using procedures that are based on theory and experience. The problem: "How do we build that experience---and catch problems---if we don't know what the true answer is?"

Blind tests like the new benchmark dataset from Chevron allow us to test and compare different methods and methodologies. Different practitioners can try to recover good approximations to the true earth model, but in this case the true (synthetic) earth model is *known*, which makes it possible to judge how different methods succeed and how they fall short. It'll be interesting to see how the results turn out!

## References

<a name="Herrmann:2013"></a>Herrmann, F. J., Hanlon, I, Kumar, R., van Leeuwen, T., Li, X., Smithyman, B., Wason, H., Calvert, A. J., Javanmehri, M. and Takougang, E. T. (2013), Frugal full-waveform inversion: From theory to a practical algorithm, The Leading Edge, 32(9), 1082–1092, doi:10.1190/tle32091082.1. [link](http://library.seg.org/doi/abs/10.1190/tle32091082.1)

<a name="Smithyman:2014"></a>Smithyman, B. R., Clowes, R. M. and Bordet, E. (2014), New geophysical models for subsurface velocity structure in the Nechako–Chilcotin plateau from 2.5-D waveform tomography, Canadian Journal of Earth Sciences, 51, 373–392, doi:10.1139/cjes-2013-0152. [link](http://www.nrcresearchpress.com/doi/full/10.1139/cjes-2013-0152)

<a name="Vigh:2008"></a>Vigh, D. and Starr, E. W. (2008), Comparisons for waveform inversion, time domain or frequency domain?, 2008 SEG Annual Meeting, pp. 1890–1894. [link](https://www.onepetro.org/conference-paper/SEG-2008-1890)

<a name="Virieux:2009"></a>Virieux, J. and Operto, S. (2009), An overview of full-waveform inversion in exploration geophysics, Geophysics, 74(6), WCC1–WCC26, doi:10.1190/1.3238367. [link](http://library.seg.org/doi/abs/10.1190/1.3238367)

[SEG]: http://www.seg.org/seg
[Chevron]: http://www.chevron.com/
[pygeo]: https://github.com/bsmithyman/pygeo

[Dataset]: https://s3.amazonaws.com/open.source.geoscience/open_data/seg_workshop_fwi_2014/seg_workshop_fwi_2014.html
[GHUB]: https://github.com/bsmithyman/IPyNB-SEG2014FWIBenchmark
[VIEW]: http://nbviewer.ipython.org/github/bsmithyman/IPyNB-SEG2014FWIBenchmark/blob/master/SEG%202014%20Chevron%20Initial.ipynb