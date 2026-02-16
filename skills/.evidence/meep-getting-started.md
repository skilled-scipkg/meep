# Evidence: meep-getting-started

## Primary docs
- `doc/docs/Introduction.md`
- `doc/docs/C++_Tutorial.md`
- `doc/bfast/fixed_angle_broadband_simulations_in_Meep.md`
- `doc/docs/Scheme_Tutorials/Basics.md`
- `doc/docs/Python_Tutorials/Basics.md`

## Primary source entry points
- `skills/meep-getting-started/references/doc_map.md`
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
- Introduction
- Units in Meep
- The Illusion of Continuity
- Field Patterns and Green's Functions
- Transmittance/Reflectance Spectra
- Resonant Modes
- C++ Tutorial
- Fixed Angle Broadband Simulations in Meep
- Scheme Tutorial
- A Straight Waveguide
- A 90° Bend
- Differential/Radar Cross Section

## Executable command hints
- $$\frac{d\mathbf{B}}{dt} = -\nabla\times\mathbf{E} - \mathbf{J}_B - \sigma_B \mathbf{B}$$
- $$\mathbf{B} = \mu \mathbf{H}$$
- $$\frac{d\mathbf{D}}{dt} = \nabla\times\mathbf{H} - \mathbf{J} - \sigma_D \mathbf{D}$$
- $$\mathbf{D} = \varepsilon \mathbf{E}$$
- $$\nabla \cdot \mathbf{B} = - \int^t \nabla \cdot (\mathbf{J}_B(t') + \sigma_B \mathbf{B}) dt'$$
- $$\nabla \cdot \mathbf{D} = - \int^t \nabla \cdot (\mathbf{J}(t') + \sigma_D \mathbf{D})dt' \equiv \rho$$
- $$G_{ij}(\omega; \mathbf{x}, \mathbf{x}')$$ which gives the $i$<sup>th</sup> component of (say) $\mathbf{E}$ at $\mathbf{x}$ from a point current source $\mathbf{J}$ at $\mathbf{x'}$, such that $\mathbf{J}(\mathbf{x})=\hat{\mathbf{e}_j} \cdot \exp(-i\omega t) \cdot \delta(\mathbf{x}-\mathbf{x}')$. To obtain this in FDTD, you simply place the requisite point source at $\mathbf{x'}$ and wait for a long enough time for all other frequency components to die out (noting that the mere act of turning on a current source at $t=0$ introduces a spectrum of frequencies). Alternatively, you can use Meep's frequency-domain solver to find the response directly (by solving the associated linear equation). For an example, see [Tutorial/Frequency Domain Solver](Python_Tutorials/Frequency_Domain_Solver.md).
- $$P(\omega) = \mathrm{Re}\, \hat{\mathbf{n}}\cdot \int \mathbf{E}_\omega(\mathbf{x})^* \times \mathbf{H}_\omega(\mathbf{x}) \, d^2\mathbf{x}$$ Now, if we input a short pulse, it is tempting to compute the integral $P(t)$ of the Poynting vector at each time, and then Fourier-transform this to find $P(\omega)$. That is **incorrect**, however, because what we want is the flux of the Fourier-transformed fields $\mathbf{E}$ and $\mathbf{H}$, which is not the same as the transform of the time-domain flux. The flux is not a linear function of the fields.
- $$\tilde{f}(\omega) = \frac{1}{\sqrt{2\pi}}  \sum_n e^{i\omega n \Delta t} f(n\Delta t) \Delta t \approx \frac{1}{\sqrt{2\pi}} \int e^{i\omega t} f(t) dt$$ and then, at the end of the time-stepping, computing $P(\omega)$ by the fluxes of these Fourier-transformed fields. Meep takes care of all of this for you automatically, of course &mdash; you simply specify the regions over which you want to integrate the flux, and the frequencies that you want to compute.
- $$P_r(\omega) = \mathrm{Re}\,\hat{\mathbf{n}}\cdot\int \left[ \mathbf{E}_\omega(\mathbf{x}) - \mathbf{E}_\omega^{(0)}(\mathbf{x}) \right]^* \times \left[ \mathbf{H}_\omega(\mathbf{x}) - \mathbf{H}_\omega^{(0)}(\mathbf{x}) \right] \, d^2\mathbf{x}$$ Again, you can do this easily in practice by running the simulation twice (using the *same* discretization, i.e., grid resolution, Courant factor, etc. and source time profile), once without and once with the scatterer, and telling Meep to subtract the Fourier transforms in the reflected plane before computing the flux. And again, after computing the reflected power you will normalize by the incident power to get the reflectance spectrum. (In both simulations, the reflected plane must be in the *same position* relative to the source so that the phases match.)
- ./foo.dac
- $\vec{C}$ an auxiliary field. When the magnetic field is redefined, the

## Warnings and pitfalls
- Perhaps the most important thing you need to know is this: if the grid has some spatial resolution $\Delta x$, then our discrete time-step $\Delta t$ is given by $\Delta t = S \Delta x$, where $S$ is the [Courant factor](https://en.wikipedia.org/wiki/Courant%E2%80%93Friedrichs%E2%80%93Lewy_condition) and must satisfy $S < n_\textrm{min} / \sqrt{\mathrm{\# dimensions}}$, where $n_\textrm{min}$ is the minimum refractive index (usually 1), in order for the method to be stable (not diverge). In Meep, $S=0.5$ by default (which is sufficient for 1 to 3 dimensions), but [can be changed](Python_User_Interface.md#the-simulation-class) by the user. This means that **when you double the grid resolution, the number of time steps doubles as well** (for the same simulation period). Thus, in three dimensions, if you double the resolution, then the amount of memory increases by 8 and the amount of computational time increases by (at least) 2.
- The second most important thing you should know is that, in order to discretize the equations with [second-order accuracy](https://en.wikipedia.org/wiki/Finite_difference_method#Accuracy_and_order), FDTD methods **store different field components at different grid locations**. This discretization is known as a [Yee lattice](Yee_Lattice.md). As a consequence, **Meep must interpolate the field components to a common point** whenever you want to combine, compare, or output the field components (e.g. in computing energy density or flux). Most of the time, you don't need to worry too much about this interpolation since it is automatic. However, because it is a simple linear interpolation, while $\mathbf{E}$ and $\mathbf{D}$ may be discontinuous across dielectric boundaries, it means that the interpolated $\mathbf{E}$ and $\mathbf{D}$ fields may be less accurate than you might expect right around dielectric interfaces.
- A strength of time-domain methods is their ability to obtain the [entire frequency spectrum of responses (or eigenfrequencies) in a single simulation](Python_Tutorials/Basics.md#transmittance-spectrum-of-a-waveguide-bend), by Fourier-transforming the response to a short pulse or using more sophisticated signal-processing methods such as [Harminv](Python_User_Interface.md#harminv). Finite-element methods can also be used for time-evolving fields, but they suffer a serious disadvantage compared to finite-difference methods: finite-element methods, for stability, must typically use some form of *implicit time-stepping*, where they must invert a matrix (solve a linear system) at every time step.
- Stability
- `pml-layers` is a list of [`pml`](../Scheme_User_Interface.md#pml) objects &mdash; you may have more than one `pml` object if you want PML layers only on certain sides of the cell, e.g. `(make pml (thickness 1.0) (direction X) (side High))` specifies a PML layer on only the $+x$ side. We note an important point: **the PML layer is *inside* the cell**, overlapping whatever objects you have there. So, in this case our PML overlaps our waveguide, which is what we want so that it will properly absorb waveguide modes. The finite thickness of the PML is important to reduce numerical reflections. For more information, see [Perfectly Matched Layer](../Perfectly_Matched_Layer.md).
- The fluxes will be computed for `nfreq=100` frequencies centered on `fcen`, from `fcen-df/2` to `fcen+df/2`. That is, we only compute fluxes for frequencies within our pulse bandwidth. This is important because, far outside the pulse bandwidth, the spectral power is so low that numerical errors make the computed fluxes useless.
- The scattering cross section ($\sigma_{scat}$) is the scattered power in all directions divided by the incident intensity. The scattering efficiency, a dimensionless quantity, is the ratio of the scattering cross section to the cross sectional area of the sphere. In this demonstration, the sphere is a lossless dielectric with wavelength-independent refractive index of 2.0. This way, [subpixel smoothing](../Subpixel_Smoothing.md) can improve accuracy at low resolutions which is important for reducing the size of this 3d simulation. The source is an $E_z$-polarized, planewave pulse (its `size` parameter fills the *entire* cell in 2d) spanning the broadband wavelength spectrum of 10% to 50% the circumference of the sphere. There is one subtlety: since the [planewave source extends into the PML](../Perfectly_Matched_Layer.md#planewave-sources-extending-into-pml) which surrounds the cell on all sides, `(is-integrated? true)` must be specified in the source object definition. A `k-point` of zero specifying periodic boundary conditions is necessary in order for the source to be infinitely extended. Also, given the [symmetry of the fields and the structure](../Exploiting_Symmetry.md), two mirror symmery planes can be used to reduce the cell size by a factor of four. The simulation results are validated by comparing with the analytic theory obtained from the [PyMieScatt](https://pymiescatt.readthedocs.io/en/latest/) module.
- The incident intensity (`intensity`) is the flux in one of the six monitor planes (the one closest to and facing the planewave source propagating in the $x$ direction) divided by its area. This is why the six sides of the flux box are defined separately. (Otherwise, the entire box could have been defined as a single flux object with different weights ±1 for each side.) The scattered power is multiplied by -1 since it is the *outgoing* power (a positive quantity) rather than the incoming power as defined by the orientation of the flux box. Note that because of the linear $E_z$ polarization of the source, the flux through the $y$ and $z$ planes will *not* be the same. A circularly-polarized source would have produced equal flux in these two monitor planes. The runtime of the scattering run is chosen to be sufficiently long to ensure that the Fourier-transformed fields have [converged](../FAQ.md#checking-convergence).
- The script is similar to the previous Mie scattering example with the main difference being the replacement of the `add-flux` with `add-near2far` objects. Instead of a linearly-polarized planewave, the source is circularly-polarized so that $\sigma_{diff}$ is invariant with the rotation angle $\phi$ around the axis of the incident direction (i.e., $x$). This way, the far fields need only be sampled with the polar angle $\theta$. A circularly-polarized planewave can be generated by overlapping two linearly-polarized planewaves ($E_y$ and $E_z$) which are 90° out of phase via specifying an `amplitude` of `0+1i` for one of the two sources. Note, however, that there is no need to use complex fields (by specifying `(set! force-complex-fields true)`) which would double the floating-point memory consumption since only the real part of the source amplitude is used by default. The circularly-polarized source breaks the mirror symmetry which increases the size of the simulation. The size of the near-field monitor box surrounding the sphere is doubled so that it lies *entirely within* the homogeneous air region (a requirement of the `near2far` feature). After the near fields have been obtained for λ = 1 μm, the far fields are computed for 100 points along a semi-circle with radius 10,000X that of the dielectric sphere. (Note: any such large radius would give the same $\sigma_{scat}$ to within discretization error.) Finally, the scattered cross section is computed by numerically integrating the expression from above using the radial Poynting flux values.
- For `resolution` of 20, the error between the simulated and analytic result is 2.2%.
- For `resolution` of 25, the error decreases (as expected) to 1.5%.
- There is one important item to note: in order to eliminate discretization artifacts when computing the $\mathbf{E}^* \cdot \mathbf{D}$ dot-product, the `add-dft-fields` definition includes `#:yee-grid true` which ensures that the $E_z$ and $D_z$ fields are computed on the Yee grid rather than interpolated to the centered grid. The DFT fields are output to an HDF5 file (`dft-fields-cylinder.h5`) at the end of the simulation.
