# Evidence: meep-examples-and-tutorials

## Primary docs
- `doc/docs/Scheme_Tutorials/Mode_Decomposition.md`
- `doc/docs/Scheme_Tutorials/Eigenmode_Source.md`
- `doc/docs/Scheme_Tutorials/Custom_Source.md`
- `doc/docs/Scheme_Tutorials/Gyrotropic_Media.md`
- `doc/docs/Scheme_Tutorials/Frequency_Domain_Solver.md`

## Primary source entry points
- `skills/meep-examples-and-tutorials/references/doc_map.md`
- `src/meepgeom.hpp`
- `src/meepgeom.cpp`
- `src/meep_internals.hpp`
- `src/meep.hpp`
- `src/fix_boundary_sources.cpp`
- `src/structure_dump.cpp`
- `src/structure.cpp`
- `src/sources.cpp`
- `src/material_data.hpp`
- `src/material_data.cpp`
- `src/h5fields.cpp`
- `src/fields_dump.cpp`
- `src/fields.cpp`
- `src/cw_fields.cpp`
- `scheme/meep_op_renames.i`
- `scheme/meep.scm.in`
- `scheme/meep.i`
- `scheme/meep.cpp`
- `scheme/meep-ctl.hpp`

## Extracted headings
- Mode Decomposition
- Transmittance Spectra for Planewave at Normal Incidence
- Reflectance and Transmittance Spectra for Planewave at Oblique Incidence
- Eigenmode Source
- What Happens When the Source Time Profile is a Pulse?
- Oblique Waveguides
- Custom Source
- Method 1: flat surface
- Method 1: textured surface
- Method 2: flat surface
- Method 2: textured surface
- Gyrotropic Media

## Executable command hints
- mpirun -np 2 meep Lt=$((2**${m})) mode-decomposition.ctl |tee -a waveguide_taper.out;
- mpirun -np 2 meep binary_grating.ctl |tee diffraction_spectra.out;
- $ meep fsrc=0.15 bnum=1 eigsource-pulse.ctl |tee singlemode-A.out
- $ grep flux1: singlemode-A.out |cut -d, -f2- > singlemode-A-flux.dat
- $ grep guided: singlemode-A.out |cut -d, -f2- > singlemode-A-guided.dat
- $ meep fsrc=0.35 bnum=2 eigsource-pulse.ctl |tee multimode-A.out
- $ grep flux1: multimode-A.out |cut -d, -f2- > multimode-A-flux.dat
- $ grep guided: multimode-A.out |cut -d, -f2- > multimode-A-guided.dat
- $ meep fsrc=0.35 bnum=1 eigsource-pulse.ctl |tee multimode-B.out
- $ grep flux1: multimode-B.out |cut -d, -f2- > multimode-B-flux.dat
- $ grep guided: multimode-B.out |cut -d, -f2- > multimode-B-guided.dat
- $$\epsilon = \begin{bmatrix}\epsilon_\perp & -i\eta \\ i\eta & \epsilon_\perp \end{bmatrix}$$

## Warnings and pitfalls
- In the reflected-flux calculation, we apply our usual trick of first performing a reference simulation with just the incident field and then subtracting that from our taper simulation with `load-minus-flux`, so that what is left over is the reflected fields (from which we obtain the reflected flux).  In *principle*, this trick would not be required for the mode-decomposition method, because the reflected mode is orthogonal to the forward mode and so the decomposition will separate the forward and reflected coefficients automatically.   However, this is only true in the limit of infinite resolution — for a *finite* resolution, the reflected mode used for the mode coefficient calculation (calculated via MPB) is not exactly orthogonal to the forward mode propagating in Meep (whose discretization scheme is different from that of MPB).   In consequence, if you did not subtract the fields of the reference simulation, the mode-coefficient could only calculate the reflected power down to a "noise floor" set by the discretization error.  With the subtraction, in contrast, you can compute much smaller reflections (limited by the floating-point precision).
- Note the use of the keyword parameter argument `#eig-parity (+ ODD-Z EVEN-Y)` in the call to `get-eigenmode-coefficients`. This is important for specifying **non-degenerate** modes in MPB since the `k-point` is (0,0,0). `ODD-Z` is for modes with $E_z$ polarization. `EVEN-Y` is necessary since each diffraction order which is based on a given k<sub>x</sub> consists of *two* modes: one going in the +y direction and the other in the -y direction. `EVEN-Y` forces MPB to compute only the +k<sub>y</sub> + -k<sub>y</sub> (cosine) mode. As a result, the total transmittance must be halved in this case to obtain the transmittance for the individual +k<sub>y</sub> or -k<sub>y</sub> mode. For `ODD-Y`, MPB will compute the +k<sub>y</sub> - -k<sub>y</sub> (sine) mode but this will have zero power because the source is even. If the $y$ parity is left out, MPB will return a random superposition of the cosine and sine modes. Alternatively, in this example an input planewave with H<sub>z</sub> instead of $E_z$ polarization can be used which requires `#eig_parity (+ EVEN-Z ODD-Y)` as well as an odd mirror symmetry plane in *y*. Finally, note the use of `add-flux` instead of `add-mode-monitor` when using symmetries.
- Results are computed for a single wavelength of 0.5 μm. The pulsed planewave is incident at an angle of 10.7°. Its spatial profile is defined using the source amplitude function `pw-amp`. This [anonymous function](https://en.wikipedia.org/wiki/Anonymous_function) takes two arguments, the wavevector and a point in space (both `vector3`s), and returns a function of one argument which defines the planewave amplitude at that point. Even though we are computing the reflectance and transmittance at a single wavelength, the choice of the bandwidth of the pulsed source is important. This is because a broad-bandwidth source generates glancing-angle waves which are poorly absorbed by the PML (or by an absorber) which in turn prevents the simulation from terminating as the fields do not decay away. The solution is to use a narrow-bandwidth pulse which limits the generation of glancing-angle waves. Because launching glancing-angle waves is generally unavoidable, the `(run-sources+ (stop-when-fields-decayed ...))` termination criteria is replaced with a fixed runtime via `(run-sources+ 200)`. As a general rule of thumb, the more oblique the planewave source, the longer the run time required to ensure accurate results. There is an additional line monitor between the source and the grating for computing the reflectance. The angle of each reflected/transmitted mode, which can be positive or negative, is computed using its dominant planewave vector. Since the oblique source breaks the symmetry in the $y$ direction, each diffracted order must be computed separately. In total, there are 59 reflected and 39 transmitted orders.
- We can also use the complex mode coefficients to compute the phase (or impedance) of the diffraction orders. This can be used to generate a phase map of the binary grating as a function of its geometric parameters. Phase maps are important for the design of subwavelength phase shifters such as those used in a metasurface lens. When the period of the unit cell is subwavelength, the zeroth-diffraction order is the only propagating wave. In this demonstration, which is adapted from the previous example, we compute the transmittance spectra and phase map of the zeroth-diffraction order (at 0°) for an $E_z$-polarized planewave pulse spanning wavelengths of 0.4 to 0.6 μm which is normally incident on a binary grating with a periodicity of 0.35 μm and height of 0.6 μm. The duty cycle of the grating is varied from 0.1 to 0.9 in separate runs.
- A schematic of the grating geometry is shown below. The grating is a 2d slab in the *xy*-plane with two parameters: birefringence (Δn) and thickness (d). The twisted-nematic grating consists of two layers of thickness d each with equal and opposite rotation angles of φ=70° for the nematic director. Both gratings contain only three diffraction orders: m=0, ±1. The m=0 order is linearly polarized and the m=±1 orders are circularly polarized with opposite chirality. For the uniaxial grating, the diffraction efficiencies for a mode with wavelength λ can be computed analytically: η<sub>0</sub>=cos<sup>2</sup>(πΔnd/λ), η<sub>±1</sub>=0.5sin<sup>2</sup>(πΔnd/λ). The derivation of these formulas is presented in [Optics Letters, Vol. 24, No. 9, pp. 584-6, 1999](https://www.osapublishing.org/ol/abstract.cfm?uri=ol-24-9-584). We will verify these analytic results and also demonstrate that the twisted-nematic grating produces a broader bandwidth response for the ±1 orders than the homogeneous uniaxial grating. An important property of these polarization gratings for e.g. display applications is that for a circular-polarized input planewave and phase delay (Δnd/λ) of nearly 0.5, there is only a single diffraction order (+1 or -1) with *opposite* chiraity to that of the input. This is also demonstrated below.
- This can be demonstrated by computing the error in a broadband eigenmode source via the backward-propagating and scattered power (i.e., any fields which are not forward-propagating waveguide modes) for the single and multi mode ridge waveguide.
- These results demonstrate that in all cases the error is nearly 0 at the center frequency and increases roughly quadratically away from the center frequency. The error tends to be smallest for single-mode waveguides because a localized source excitation couples most strongly into guided modes. Note that in this case the maximum error is ~1% for a source bandwidth that is 67% of its center frequency.  For the multi-mode waveguide, a much larger scattering loss is obtained for the higher-order mode **A** at frequencies below the center frequency, but this is simply because that mode ceases to be guided around a frequency `≈ 0.3`, and the mode pattern changes dramatically as this cutoff is approached.
- We demonstrate that the total power in a waveguide with *arbitrary* orientation — computed using two equivalent methods via `get_fluxes` and [mode decomposition](../Mode_Decomposition.md) — can be computed by a single flux plane oriented along the $y$ direction: thanks to [Poynting's theorem](https://en.wikipedia.org/wiki/Poynting%27s_theorem), the flux through any plane crossing a lossless waveguide is the same, regardless of whether the plane is oriented perpendicular to the waveguide. Furthermore, the eigenmode source is normalized in such a way as to produce the same power regardless of the waveguide orientation — in consequence, the flux values for mode **A** of the single-mode case for rotation angles of 0°, 20°, and 40° are 1111.280794, 1109.565028, and 1108.759159, within 0.2% (discretization error) of one another.
- There are five items to note in this script. (1) The frequency discretization of the flux spectrum must be sufficiently fine to resolve noisy features. In this example, the frequency range is 0.9 to 1.1 with spacing of 0.0004. (2) The runtime must be long enough for the DFT spectrum to resolve these oscillations. Due to the Fourier Uncertainty Principle, the runtime should be at least ~1/frequency resolution. Here, we found that a larger runtime of 2/frequency resolution was sufficient to [converge](../FAQ.md#checking-convergence) to the desired accuracy.  Technically, what we are doing is [spectral density estimation](https://en.wikipedia.org/wiki/Spectral_density_estimation) of the [periodogram](https://en.wikipedia.org/wiki/Periodogram) by an ensemble average with a [rectangular window](https://en.wikipedia.org/wiki/Window_function#Rectangular_window), but many variations on this general idea exist.  (3) The material properties for Ag are imported from the [materials library](../Materials.md#materials-library). (4) At the end of each run, the flux spectra is printed to standard output. This data is used to plot the results in post processing. (5) For Method 1, independent random numbers can be used for the white-noise dipole emitters in the trial runs for the two cases of a flat and textured surface, since all that matters is that they average to the same power spectrum.
- *Note regarding convergence properties of Method 2:* In this demonstration, the number of point dipoles used in Method 2 is one per pixel. However, because this example is a unit-cell calculation involving *periodic* boundaries, the number of point dipoles (equivalent to the number of simulations) that are actually necessary to obtain results with sufficient accuracy can be significantly reduced. For smooth periodic functions, it is well known that a [trapezoidal rule](https://en.wikipedia.org/wiki/Trapezoidal_rule) converges quite fast — generally even faster than a cosine-series expansion and comparable to a cosine+sine Fourier series. See these [tutorial notes](http://math.mit.edu/~stevenj/trap-iap-2011.pdf) for the mathematical details. In this example, placing one dipole at every fifth pixel for a total of 15 rather than 75 simulations produces nearly equivalent results for the flux spectrum. More generally, an alternative approach for Method 2 would be to sample a set of dipole points and repeatedly double the sampling density until it converges. Sampling every grid point is usually not necessary.
- *Note regarding polarization:* The previous demonstration involved a single-polarization source. For random polarizations, three separate simulations (for $\mathcal{J}_x$, $\mathcal{J}_y$, and $\mathcal{J}_z$) are required. Since the different polarizations are uncorrelated, the results (i.e., the flux spectrum) from each set of single-polarization simulations (which, in general, will have different convergence properties) are then summed in post processing. If the emitters involve *anisotropic* polarizability then the polarizations are correlated. However, in this case choosing the polarizations to correspond to the three principal axes will again make them uncorrelated.
- This tutorial demonstrates Meep's [frequency-domain solver](../Scheme_User_Interface.md#frequency-domain-solver) which is used to compute the fields produced in a geometry in response to a [continuous-wave (CW) source](https://en.wikipedia.org/wiki/Continuous_wave). For details on how this feature works, see Section 5.3 ("Frequency-domain solver") of [Computer Physics Communications, Vol. 181, pp. 687-702, 2010](http://ab-initio.mit.edu/~oskooi/papers/Oskooi10.pdf). This example involves using the frequency-domain solver to compute the fields of a ring resonator which has been described in [Tutorial/Basics/Modes of a Ring Resonator](Basics.md#modes-of-a-ring-resonator). We will verify that the error in the computed fields decreases monotonically with decreasing tolerance of the iterative solver.
