# Colors, Spectra and Display

Various ratios of activation of three types of cone cells in our eyes that are responsive to long, medium and short wavelength bands corresponding to overlapping red, green and
blue wavelength bands produce all perceptible color sensations in humans. These curves of these activation sensitivities overlap. This the basis of the phenomenon of metamerism.

Display hardware target this fact to have 2D arrays of physical pixels, each pixel having a red, blue and green leds to stimulate our cone cells. These leds are designed to
emit light at narrow ranges of wavelengths that have peak intensity at specific wavelengths of visible spectrum, the particular target wavelengths of each led correspond to
the primaries of the color profile that display is built for, e.g sRGB.

The 1931 CIE XYZ color space, is a foundational 3D space of all human perceptible colors. This space is defined by using color matching functions(CMFs)
that are derived from measuring human eye response to different wavelengths. These functions help mapping EMR values to XYZ space, where Y is luminance and
ratios of X, Y and Z together give chromaticity. Chromaticity can be explicitly visualized after projecting the space into
xy chromaticity diagram i.e normalizing X,Y values w.r.t X+Y+Z, and then using xy coordinates. Thus it factors in metamerism, i.e different spectral distributions
pointing to same color in the space. The CIE XYZ space bridges EMR to perceptible color. 
The XYZ tristimulus values (X, Y, Z) are proportional to radiometric quantities, specifically related to spectral radiance integrated against color matching functions.
While we often work with normalized XYZ values in color space computations, in a physically based rendering context, they would be proportional to
radiance, so having units of W/m^2/st. Y is directly proportional to luminance, so units of nits. The real spectral distribution of a percieved color cannot be recreated
using the CIE XYZ space, but a candidate SPD amongst the infinite possible candidates may be generated to reproduce the same XYZ value i.e same color sensation.
The XYZ space can be projected into a 2D xy chromaticity diagram where the x, y coordinates give the hue and saturation (chroma). This diagram is unitless since its derived
from ratios of X,Y and Z and uses coordinates(x,y).

sRGB is a color profile, or color space designed with respect to the CIE XYZ space. The sRGB color profile has defined primaries for red, green,
blue by using coordinates from CIE XYZ space, forming a triangle gamut(a subset of the graph area) that encompasses the possible colors that sRGB can represent.
Another coordinate is used to represent the white-point of the sRGB color space, which is D65, i.e ISO defined standard daylight illuminant color temperature = 6500K.
Relation of color to temperature can be explained via emission spectra of black body emitters. sRGB also provides encoding and decoding functions for non-linear storage
of color values, for efficient, preserving, high quality storage of color data given non-linear sensitivity of human eye to luminance. It devotes higher precision
to darker end of luminance values and the inverse for brighter end of luminance values, to account for increased sensitivity of human vision to lower luminance.
It also enables the resultant emission from the hardware to appear linear to human eyes due to non-linear sensitivity of human eye to luminance.

Converting from a normalized sRGB to linear space is done by applying the sRGB decoding function to the normalized, sRGB value. The resultant linear value is also normalized.
Converting from unbounded linear HDR to normalized linear LDR is done by applying a color transform, also called a tonemapping operator. Tonemappers map HDR to LDR RGB color
data by applying curves to smoothly transition from saturated(out of representable range) to differentiable ranges, depending upon exposure parameters.
The contrary is abrupt clipping of RGB values. 

Sophisticated tonemappers like AgX also have min EV, max EV parameters, also referred to as black-point and white-point, which control the dynamic range of the tonemapper.
The values of blackpoint, dynamic ranges etc are represented in EV unit, which is a non-linear unit of luminance, used when working with camera controls, which
is logarithmic(base 2) in nature to represent the way human eyes perceive relative change in luminance. 
Here 1 EV is 1 stop of light which always appears as an equal change in luminance to human eyes but is actually doubling luminance each step. EV units can
be converted to a relative linear luminance change factor by simply 2^EV = luminance factor. This is not a physical quantity of luminance, but rather a factor, that can
be applied to a real measured reference luminance value to compute the luminance value at different of stops of light, relative to the reference luminance value.
This logarithmic(base 2) encoding of luminance is also known as log space encoding.

The luminance of an RGB triplet is found by summing the product of its components with respective luminance coefficients, which are provided by
the color space, e.g rec 709(with same coefficients as sRGB). 

HDR (also called unbounded) linear RGB can be computed from a spectral distribution(also called SPD), by integrating over the SPD band. The integral
is product of the contribution of the wavelength to the triplet component and the power value in that wavelength. This is done for each component of the triplet.

The contribution of a wavelength to a component is given by CMF for the component, which is a function of wavelength, and gives a 0.0-1.0 value
for XYZ space in standard applications. In very carefully specific, narrow and crafted circumstances, a 3 component type storing radiance values
in 3 discrete wavelengths corresponding to RGB primaries of the hardware, then the RGB triplet could be approximated by directly multiplying this 3 component type
with a 3x3 matrix that stores precomputed CMF values.

The resultant SPD of a path-tracing operation is a distribution of radiance (W/m^2/st/nm) over wavelengths of the visible spectrum. Here the power of the SPD
actually describes the spectral radiance of the wavelength.

Performing lighting calculations in RGB instead of SPD implicitly means quantizing the would-be SPD into three broad channels with wavelengths relating
to hardware primaries(R,G,B)(i.e implicitly assuming integration w.r.t CMFs). As well as disallowing a ray to have independent, wavelength-based behaviour(dispersion),
due to implicit aggregation of would-be wavelengths into three broad channels of the single 3-pseudo-primary-wavelength-representative tristimulus aggregate type(e.g float3).
<hr>
