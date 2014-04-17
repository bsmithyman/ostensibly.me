---
title: About Me
description: Basic information and research profile
date: "2014-04-16"
---

My name is Brendan Smithyman. I'm a geophysicist working on seismic full-waveform inversion, but I have very broad interests. I am currently (as of March, 2014) a postdoctoral fellow at the [Seismic Laboratory for Imaging and Modeling](https://www.slim.eos.ubc.ca/), at [UBC](http://www.ubc.ca/). You can find some of my work [on GitHub](https://github.com/bsmithyman).

## Research Profile

I am a postdoctoral research fellow doing research in geophysics at the University of British Columbia, in the Department of Earth, Ocean and Atmospheric Sciences. The main topic of my research is full-waveform inversion of complex field seismic data. I use advanced processing techniques to build detailed models of the subsurface (of the earth) in terms of the seismic velocity of rocks (which is conceptually similar to the sound speed), and their signal attenuating properties. Combined with the use of controlled sources of vibrations, information can be recorded that is useful in determining the structure of the shallow earth.

What follows below is a somewhat more technical overview of my PhD research. However, this is substantially simplified for the sake of brevity and is not meant to provide a full overview of the field.

### 2.5D Waveform Tomography

The goal of our work is to develop useful, interpretable images of the Earth's subsurface, especially in terms of P-wave velocity and viscoacoustic attenuation, using advanced seismic imaging techniques. We apply a technique known as waveform tomography, wherein high-quality traveltime data are processed by traveltime (tomographic) inversion followed by frequency-domain, two-dimensional (2.5D) viscoacoustic full-waveform inversion of preconditioned waveform data. This technique takes advantage of the reduced nonlinearity of the traveltime-inversion objective function compared to the objective function found in full-waveform inversion. Ideally the velocity model resulting from traveltime inversion is similar to a low-wavenumber model using wave-equation methods. By careful use of traveltime-inversion techniques, a starting model can be produced that is designed to be close to the global minimum of the eventual solution.

The application of full-waveform inversion requires that the characteristics of the survey be reproduced accurately when generating synthetic data (forward modeling). This is simplest in cases where geometry and survey characteristics are regular or easily controlled; examples include synthetic studies, marine acquisition and cross-hole experiments. Some problems exist that do not lend themselves to a 2D solution; this could be because the structures in the earth are too complex, or because the terrain and survey parameters make it impossible to collect the appropriate data. This project develops a good way to solve this second problem, using a technique known as 2.5D full-waveform inversion.

Land acquisition in general, and vibroseis acquisition in particular, is difficult to simulate in a 2D full-waveform modeling code, because topographic features and land-use concerns often control the placement of source points and receiver groups. The incorporation of off-line (y-direction) station offsets is often unavoidable, and the irregular geometries produce effects in the data that cannot be modeled correctly with a 2D processing workflow. The application of 2.5D waveform tomography avoids some of these issues, because it can model 3D survey geometry.

In many cases when "2D" seismic acquisition is used, the geometry encoded into the waveform information may not be close enough to planar to expect 2D full-waveform inversion to converge using static-corrected waveforms. However, 2.5D full-waveform inversion simulates 3D geometric spreading, and resolves this limitation. Long source-receiver offsets—useful in normal circumstances to improve the aperture of illumination of seismic targets—make approximating the geometry very difficult. However, the absence of cross-line information (i.e. information perpendicular to the "2D" plane of acquisition) means that the benefits of carrying out 3D full-waveform inversion are not sufficient to warrant the computational cost. In this case, 2.5D full-waveform inversion provides the best balance of effectiveness and computational efficiency. This makes use of repeated solutions of the 2D wave equation, but combines the results to achieve most of the benefits of 3D wave modeling.
