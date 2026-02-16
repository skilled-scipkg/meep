# Evidence: meep-simulation-workflows

## Primary docs
- `doc/docs/index.md`
- `doc/docs/The_Run_Function_Is_Not_A_Loop.md`
- `doc/docs/Python_User_Interface.md`
- `doc/docs/Python_Developer_Information.md`
- `doc/docs/Parallel_Meep.md`
- `doc/docs/Field_Functions.md`
- `doc/docs/Synchronizing_the_Magnetic_and_Electric_Fields.md`
- `doc/docs/Scheme_Tutorials/Near_to_Far_Field_Spectra.md`
- `doc/docs/Python_Tutorials/Gyrotropic_Media.md`

## Primary source entry points
- `skills/meep-simulation-workflows/references/doc_map.md`
- `src/fields_dump.cpp`
- `src/fields.cpp`
- `src/cw_fields.cpp`
- `src/time.cpp`
- `src/step_generic.cpp`
- `src/step_db.cpp`
- `src/step.cpp`
- `src/h5fields.cpp`
- `src/loop_in_chunks.cpp`
- `src/near2far.cpp`

## Extracted headings
- Discussion Forum
- Bug Reports and Feature Requests
- Python User Interface
- Simulation
- Output File Names
- Simulation Time
- Field Computations
- Reloading Parameters
- Flux Spectra
- Mode Decomposition
- DiffractedPlanewave
- Energy Density Spectra

## Executable command hints
- $\phi$ dependence of the fields is of the form $e^{im\phi}$ (default is `m=0`).
- $\exp(i\mathbf{k}\cdot\mathbf{R})$ times the fields at the other side, separated
- $k_z$ is incorporated as an additional term in Maxwell's equations, which still
- $S$ which relates the time step size to the spatial discretization: $cΔ t = SΔ x$.
- $f$ (in Meep units) from the `geometry` exactly at each grid point. `frequency` defaults to 0 which is
- $\varepsilon$ by bilinearly interpolating from the nearest Yee grid points. This function is useful for
- $\Re [\mathbf{E}^* \times \mathbf{H}]$) in that volume. Most commonly, you specify
- $\mathbf{E}^* \cdot \mathbf{D}/2$ in the given volume. If the volume has zero size
- $\mathbf{H}^* \cdot \mathbf{B}/2$ in the given volume. If the volume has zero size
- $\left(\int\varepsilon|\mathbf{E}|^2\right)/\left(\max{\varepsilon|\mathbf{E}|^2}\right)$.
- $f(\mathbf{x},c_1,c_2,\ldots)$ of position $\mathbf{x}$ and various field
- $$ \frac{1}{2}ε|\mathbf{E}|^2 + \frac{1}{2}μ|\mathbf{H}|^2 $$

## Warnings and pitfalls
- For a list of topics, see the left navigation sidebar. For new users, the most important items to review are the [Introduction](Introduction.md), [Tutorial/Basics](Python_Tutorials/Basics.md), and [FAQ](FAQ.md).
- **This is wrong.**  It will output "Hello World!" *once*, then give an error.  What is going on? The problem is that you are thinking of `run` (Python) or `run-until` (Scheme) in the wrong way, as if it were a loop:
- Two things went wrong.  First, the arguments are evaluated **before** calling the function, which means that the `print` statement is executed before `run-until` even starts. Second, `run-until` then tries to call the **result** of `(print ...)` as if it were a function, which causes an error because `(print ...)` does not return a function. The `print` returns a special Scheme code `#<unspecified>` that means it doesn't really return anything at all.
- usually ensures stability with the default Courant factor of 0.5, at the expense
- of slowing convergence of the fields near $r=0$.
- the actual FDTD error. Disabling subpixel averaging will lead to [staircasing
- convergence](Subpixel_Smoothing.md#what-happens-when-subpixel-smoothing-is-disabled).
- Default is 0.5. For numerical stability, the Courant factor must be *at
- differences in roundoff error from making your results different by one timestep
- from machine to machine (a difference much bigger than roundoff error); in this
- Given a bunch of [`FluxRegion`](#fluxregion) objects, you can tell Meep to accumulate the Fourier transforms of the fields in those regions in order to compute the Poynting flux spectra. (Note: as a matter of convention, the "intensity" of the electromagnetic fields refers to the Poynting flux, *not* to the [energy density](#energy-density-spectra).) See also [Introduction/Transmittance/Reflectance Spectra](Introduction.md#transmittancereflectance-spectra) and [Tutorial/Basics/Transmittance Spectrum of a Waveguide Bend](Python_Tutorials/Basics.md#transmittance-spectrum-of-a-waveguide-bend). These are attributes of the `Simulation` class. The most important function is:
- Each `Near2FarRegion` is identical to `FluxRegion` except for the name: in 3d, these give a set of planes (**important:** all these "near surfaces" must lie in a single *homogeneous* material with *isotropic* ε and μ &mdash; and they should *not* lie in the PML regions) surrounding the source(s) of outgoing radiation that you want to capture and convert to a far field. Ideally, these should form a closed surface, but in practice it is sufficient for the `Near2FarRegion`s to capture all of the radiation in the direction of the far-field points. **Important:** as for flux computations, each `Near2FarRegion` should be assigned a `weight` of &#177;1 indicating the direction of the outward normal relative to the +coordinate direction. So, for example, if you have six regions defining the six faces of a cube, i.e. the faces in the $+x$, $-x$, $+y$, $-y$, $+z$, and $-z$ directions, then they should have weights +1, -1, +1, -1, +1, and -1 respectively. Note that, neglecting discretization errors, all near-field surfaces that enclose the same outgoing fields are equivalent and will yield the same far fields with a discretization-induced difference that vanishes with increasing resolution etc.
