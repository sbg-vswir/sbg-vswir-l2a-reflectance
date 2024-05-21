**SBG VSWIR Snow Physics Products – Algorithm Theoretical Basis Document (ATBD)**

**D. R. Thompson*<sup>1</sup>

<sup>1</sup>Jet Propulsion Laboratory, California Institute of Technology

Corresponding author: David R. Thompson (david.r.thompson@jpl.nasa.gov)

**Key Points:**

**Version:** 1.0

**Release Date:** TBD

**DOI:** TBD

# Abstract

# Plain Language Summary

## Keywords: snow fractional cover, snow grain size, snow albedo

# 1 Version Description

This is Version 1.0 of the SBG VSWIR snow physics algorithms.

# 2 Introduction

# 3 Context/Background

## 3.1 Historical Perspective

## 3.2 Additional Information

# 4 Algorithm Description

![img.png](figs/img.png)

**Figure 1.** _Map of snow physics SRR core product algorithms._

## 4.1 Multiple Endmember Snow Cover and Grain Size (MEMSCAG)

### 4.1.1 Scientific theory

MEMSCAG features a joint estimation of snow grain size and fractional cover by coupling a spectral unmixing approach with a radiative transfer model. Both the number of endmembers and the endmembers themselves are flexible on a per-pixel basis to address spatial heterogeneity. In its default version, MEMSCAG uses a spectral library containing three types of surface endmembers: snow, vegetation, and rock. These can be extended, if desired, by soil and lake ice spectral endmembers.

Snow endmembers are simulated by combining Mie scattering and the discrete-ordinates radiative transfer model (DISORT) (Stamnes et al., 1988) for grain radii of 10 – 1100 µm, with steps of 10 µm. The simulations include variations with respect to differing solar geometry and diffuse and direct components of irradiance, and represent the hemispherical-directional reflectance factor (HDRF):

,(1)

where and are zenith and azimuth angles, and the subscripts 0 and r signify incident and reflected. is reflected radiance, is the direct, and is the diffuse irradiance illuminating the surface. Endmembers for all other surface types are derived from ASD spectral measurements in the field, which are then transformed into HDRF using Equation 1.

MEMSCAG analyzes linear spectral mixtures for all possible combinations of two or more endmembers by fitting a set of linear equations to the HDRF measured by the instrument. The linear spectral mixture model is expressed as:

,(2)

where is the measured HDRF, is the fraction of endmember _i_, is the HDRF of endmember _i_, and is the residual error at wavelength . The system of equations is then solved by modified Gram-Schmidt orthogonalization (see Section 4.1.2). The residual error is expressed accordingly:

.(3)

As goodness-of-fit criterion, MEMSCAG uses the root mean squared error (RMSE) as suggested by Painter et al. (1998) and Roberts et al. (1998):

,(4)

where _M_ is the number of instrument bands. As final step, MEMSCAG normalizes the estimated snow fractional cover by the additive complement of the shade fraction to account for topographic effects on irradiance:

.(5)

For the selection of valid mixture models, MEMSCAG applies specific constraints:

1. No more than one endmember from a surface class is present.
2. The spectral fractions sum to 1.0.
3. Spectral fractions are in the range [-0.01, 1.01].
4. Overall RMSE \< 2.5%.
5. No seven consecutive residuals (i.e., for consecutive wavelengths) exceed 2.5%.

From all _n_-endmember collections that meet the constraints, MEMSCAG selects the models with the lowest RMSE and then assigns snow fraction and grain size from the model with the fewest endmembers.

### 4.1.1.1 Scientific theory assumptions

MEMSCAG applies the following assumptions and limitations:

1. At-sensor radiance is a linear combination of radiances reflected from individual surfaces, i.e., linear spectral unmixing is a valid approach.
2. is the additive complement to the sum of the physical spectral fractions (i.e., )
3. The surface is flat and horizontal, i.e., Lambertian.
4. Variability in HDRF for a given solar geometry is negligible within the range of possible reflectance angles that occur under realistic acquisition conditions.
5. Light-absorbing particles (LAP) and liquid water in snow as well as thin snow do not impact the retrieval of snow fraction and grain size.
6. Effects of sub-pixel surface roughness on the HDRF of snow are negligible.
7. Vegetation canopy is snow-free.

### 4.1.2 Mathematical theory

In the following, all uppercase letters represent matrices, while lowercase letters typify vectors. MEMSCAG solves the set of linear equations in the mixture model by modified Gram-Schmidt orthogonalization. First, it finds the orthogonal and upper-triangular matrices to the transpose of the spectral library dataset matrix by applying QR-factorization, such that

,(6)

where . The orthogonal matrix _Q_ is then used to compute the regression coefficients _b_ based on a given measurement _y_:

,(7)

where _y_ = . Finally, the endmember fractional cover values _p_ are calculated using both _Q_ and the upper-triangular matrix _R_:

.(8)

### 4.1.3 Algorithm Input Variables

**Table 2.** _Input variables to MEMSCAG_

| Name | Description | Unit | Required |
| --- | --- | --- | --- |
| HDRF | hemispherical-directional reflectance factor per wavelength | unitless | true |
| Endmember Spectral Library | collection of spectral surface endmembers (HDRF per wavelength) | unitless | true |
| Observation Geometry | solar zenith angle, view zenith angle, relative azimuth angle | degree | false |
| Solar Irradiance | direct and diffuse downwelling solar irradiance per wavelength |
 | false |

### 4.1.4 Algorithm Output Variables

**Table 2.** _Output variables from MEMSCAG_

| Name | Description | Unit |
| --- | --- | --- |
| Snow Fractional Cover | sub-pixel snow fractional cover | % |
| Snow Grain Size | effective radius of snow grains |
 |
| Spectral Residuals | deviation between measured and modeled HDRF per wavelength | % |
| RMSE | root mean square error for the spectral residuals | % |

**4.2**  **Nolin/Dozier Model**

4.2.1 Scientific theory

The Nolin/Dozier model is an alternative to MEMSCAG in terms of estimating snow grain size. However, it does not include the calculation of snow fractional cover. The method uses the scaled ice absorption band area around 1030 nm and relates it to snow grain radius obtained from a look-up-table (LUT). The scaled absorption band area is a dimensionless quantity and calculated by integrating the scaled absorption band depth over the wavelengths of the ice absorption feature:

,(9)

where is the continuum reflectance, and is the measured reflectance spectrum. The continuum end points are determined by averaging pairs of reflectance values at both 950 and 960 nm, and 1080 and 1090 nm. The integral of Equation 9 is then calculated using the trapezoidal rule.

The LUT is calculated by using the same models and input variables as for the MEMSCAG algorithm: a combination of Mie scattering and DISORT for varying grain radii (50 – 1000 µm), solar illumination angles, as well as direct and diffuse irradiances.

###

### 4.2.1.1 Scientific theory assumptions

### 4.2.2 Mathematical theory

### 4.2.2.1 Mathematical theory assumptions

### 4.2.3 Algorithm Input Variables

### 4.2.4 Algorithm Output Variables

**4.3** **Imaging Spectrometer-Snow Albedo and Radiative Forcing (IS-SnARF)**

### 4.3.1 Scientific theory

### 4.3.1.1 Scientific theory assumptions

### 4.3.2 Mathematical theory

### 4.3.2.1 Mathematical theory assumptions

### 4.3.3 Algorithm Input Variables

### 4.3.4 Algorithm Output Variables

## 4.1 Scientific Theory

### 4.1.2 Scientific theory assumptions

## 4.2 Mathematical Theory

### 4.2.1 Mathematical theory assumptions

## 4.3 Algorithm Input Variables

## 4.4 Algorithm Output Variables

# 5 Algorithm Usage Constraints

# 6 Performance Assessment

## 6.1 Validation Methods

## 6.2 Uncertainties

## 6.3 Validation Errors

# 7 Algorithm Implementation

## 7.1 Algorithm Availability

## 7.2 Input Data Access

Candidate EMIT snow scenes:

- EMIT\_L2A\_RFL\_001\_20230216T211823\_2304714\_007
- EMIT\_L2A\_RFL\_001\_20230220T102026\_2305107\_004
- EMIT\_L2A\_RFL\_001\_20230220T194126\_2305113\_011
- EMIT\_L2A\_RFL\_001\_20230323T144706\_2308209\_038

## 7.3 Output Data Access

## 7.4 Important Related URLs

# 8 Significance Discussion

# 9 Open Research

# 10 Acknowledgements

# 11 Contact Details

# References
