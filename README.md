## ConfocalGN

# Overview

ConfocalGN is a MATLAB program that can simulate confocal imaging.
Synthetic confocal image stacks can serve as test images for an image analysis
program, allowing one to compare the output of the analysis with the 'ground truth'
provided to built the synthetic images.

ConfocalGN starts from a ground truth specified as a 3D image matrix of high resolution. 
This image is pixelized, and convolved with a Gaussian approximation of the PSF 
of the confocal microscope. 
Noise is also added, but ConfocalGN can automatically extract the characteristics
of the noise from a sample confocal image, to ensure that the simulated images
possess the same noise characteristics as the real images.

In addition, ConfocalGN may optionally include:
- Fluorophore stochasticity
- Background fluorescence
- Pixel (sensor) noise 

# Requirements

To use the confocal generator, the user must provide :

1 - A ground truth image IMG (a 3D matrix or a TIFF file)
The ground truth image should be a high resolution image. By default the ground truth image
should be isotropic, but non-isotropic images are possible provded CONF.pix and CONF.psf are 
scaled accordingly. 

2 - A parameter structure CONF, containing:
-- CONF.pix : voxel size (in units of the pixel size of IMG), a 3x1 vector
-- CONF.psf : parameters of the point spread function (in units of the pixel size of IMG)
This is a 3x1 vector; containing the standard deviation of the PSF in each direction.
The PSF is assumed then to be a 3D Gaussian built from these parameters.
The PSF deviations can be obtained from PSF simulating software(e.g. Huygens/Icy) 
or from analysis of experimental data (e.g. with Huygens/Mosaic)

3A - A noise distribution NOISE and a signal value SIG    -- or 

3B - A sample image (array or image name) SAMPLE for the program to derive NOISE and SIG
    ConfocalGN segments the image (using a default or user-provided segmentation function)
    The pixels above the threshold are considered as signal, the others as background
NOISE is a 3x1 vector of the 3 first moments of the background pixel values
SIG is the mean of the signal pixel values


Optionally, the user can also specify:
- Sampling and segmentation options by editing the file confocal_options.m
- Custom segmentation program by replacing the file segment_image.m with another equivalent segmentation method
    The segmentation program is used to recognize background from signal
    The replacing function must be of the format [ img,mask] = segment_image(image,options)
    see segment_image for the definitions of img,mask,image,options

# Operating ConfocalGN

To operate ConfocalGN, open MATLAB and switch to the ConfocalGN directory.
- run cgn_startup.m (this will update the MATLAB path)

Use the appropriate program to simulate confocal imaging, depending if you use option A or B:

- `stack_generator` to use the parameters 3A

[stacks,offset,achieved_sig,achieved_noise,im]=stack_generator(IMG,CONF,NOISE,SIG);

- `confocal_generator` to use the parameters 3B

[stacks,offset,acheived_sig,achieved_noise,im]=confocal_generator(IMG,CONF,SAMPLE);

The output is the same in both case:

- `stacks` : simulated image stack
- `offset` : translation needed to align with IMG
- `acheived_sig` : mean signal value in the simulated image
- `achieved_noise` : noise distribution in the simulated image
- `im` : post-segmentation image of stack


# Example :

Open MATLAB
Switch current directory to the ConfocalGN main directory
In the MATLAB command window, run:

cgn_startup.m 
cgn_example.m


# Credits & Licence

Code is distributed under GPL3.0 Licence (see LICENCE.md)
This code includes the librairies :
- tiffread Copyright (C) 1999-2010 Francois Nedelec
- gausss3filter Copyright (C) Max W.K. Law
- Octave code (Copyright (C) 2006-2015 John W. Eaton), under GPL licence
- saveastiff, (Copyright (c) 2012, YoonOh Tak)

Code modified from GNU Octave : 
- randg
- gamrnd
see tiffread.m ; octave_randg.m ; gamrnd_simpl.m for licencing information.

This code was developped by Serge Dmitrieff in 2016 in EMBL, with the support of François Nédélec.
http://biophysics.fr

# Changelog
-June 2016 : initial minimal version
-July 2016 : correction of stacking
-July 2016 : major overhaul for easier use, and usage of sample image

# Source for gauss3filter 
Max W. K. Law and Albert C. S. Chung, "Efficient Implementation for Spherical Flux Computation and Its Application to Vascular Segmentation",
IEEE Transactions on Image Processing, 2009, Volume 18(3), 596�V612
