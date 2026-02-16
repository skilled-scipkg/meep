# Evidence: meep-api-and-scripting

## Primary docs
- `doc/docs/Mode_Decomposition.md`
- `doc/docs/Python_Tutorials/Near_to_Far_Field_Spectra.md`
- `doc/docs/Python_Tutorials/Eigenmode_Source.md`
- `doc/docs/Python_Tutorials/Cylindrical_Coordinates.md`
- `doc/docs/Python_Tutorials/Adjoint_Solver.md`
- `doc/docs/Python_Tutorials/Optical_Forces.md`
- `doc/docs/Python_Tutorials/Frequency_Domain_Solver.md`

## Primary source entry points
- `skills/meep-api-and-scripting/references/doc_map.md`
- `src/near2far.cpp`
- `scheme/meep_op_renames.i`

## Extracted headings
- Mode Decomposition
- Theoretical Background
- Function Description
- Normalization
- Related Functions
- Computing MPB Eigenmodes
- Working with MPB Eigenmodes
- Exporting Frequency-Domain Fields
- Computing Overlap Integrals
- How Mode Decomposition Works
- Near to Far Field Spectra
- Analytic formulas for the radiation pattern of an electric dipole

## Executable command hints
- $$
- $\alpha^{\pm}_n$ are the expansion coefficients (the amplitudes of each mode present in the fields). Mode decomposition involves solving for these amplitudes. The following steps are involved in the computation:
- $$|\alpha_n^\pm|^2 = P_n^\pm$$
- $$ \left\langle \psi , \psi' \right\rangle
- $\mathbf{E}^+_{n\parallel} =+\mathbf{E}^-_{n\parallel}$
- $$ |\textit{power}_n^\pm| = |\alpha_n^\pm|^2 $$
- $$P_{total} = \int_0^{2\pi} \int_0^{\frac{\pi}{2}} P(\theta) r^2 \sin(\theta) d\theta d\phi = 2 \pi r^2 \sum_{n=0}^{N-1} P(\theta_n) \sin(\theta_n) \Delta \theta$$
- $$P(\theta) \approx \int_0^R P(r,\theta) s(r) 2\pi rdr = \sum_{n=0}^{N-1} P(r_n,\theta) s(r_n) 2\pi r_n \Delta r$$,
- $E_z$ for $\omega$=0.118 $m$=3 mode:
- $E_z$ for $\omega$=0.148 $m$=4 mode:
- $E_z$ for $\omega$=0.176 $m$=5 mode:
- $$ \Delta\omega = -\frac{\omega}{2} \frac{ \iint d^2 \vec{r} \big[ (\varepsilon_1 - \varepsilon_2) |\vec{E}_{\parallel}(\vec{r})|^2 - \big(\frac{1}{\varepsilon_1} - \frac{1}{\varepsilon_2}\big)|\varepsilon\vec{E}_{\perp}|^2\big] \Delta h}{\int d^3\vec{r} \varepsilon(\vec{r})|\vec{E}(\vec{r})|^2} + O(\Delta h^2) $$

## Warnings and pitfalls
- print(f"error:, {rel_err:.6f}")
- The total flux computed using the near and far fields is shown to be in close agreement with a relative error of ~7%.
- total_flux:, 643.65058 (near), 595.80239 (far), 7.43% (error)
- The error decreases with increasing (1) grid resolution, (2) runtime, and (3) number of angular grid points. However, this only applies to a *closed* near-field surface which is not the case in this example. This is because the ground plane, which extends to infinity, contains $H_r$ and $H_\phi$ fields on its surface which are not zero (unlike the $E_r$ and $E_\phi$ fields). These magnetic fields produce equivalent currents which radiate into the far field. The PML in the $r$ direction does not mitigate this effect.
- Because the near-field surface actually extends to infinity in the $r$ direction, one approach to reducing the error introduced by its finite truncation would be to simply make the cell size in the $r$ direction larger (the parameter `cell_r_um` in the script below). Another option which would remove this error entirely would be to simulate the same structure using a closed surface by removing the ground plane and duplicating the structure and source below the $z = 0$ plane. This is known as the method of images. See [Tutorial/Antenna above a Perfect Electric Conductor Ground Plane ](#antenna-above-a-perfect-electric-conductor-ground-plane) for a demonstration of this approach.
- f"{100 * err:.2f}% (error)"
- Note: in the case of a disc, the set of dipoles within the quantum well (QW) which spans a 2D surface only needs to be computed along a line. This means that the number of single-dipole simulations necessary for convergence is the same in cylindrical and 3D Cartesian coordinates.
- In the second part of the calculation, the far-field energy-density profile of three supercell lens designs, comprised of 201, 401, and 801 unit cells, are computed using the quadratic formula for the local phase. Initially, this involves fitting the unit-cell phase data to a finer duty-cycle grid in order to enhance the local-phase interpolation of the supercell. This is important since as the number of unit cells in the lens increases, the local phase via the duty cycle varies more gradually from unit cell to unit cell. However, if the duty cycle becomes too gradual (i.e., less than a tenth of the pixel dimensions), the `resolution` may also need to be increased in order to improve the accuracy of [subpixel smoothing](../Subpixel_Smoothing.md).
- Modeling a finite grating requires specifying the `nperiods` parameter of `add_near2far` which sums `2*nperiods+1` Bloch-periodic copies of the near fields. However, because of the way in which the edges of the structure are handled, this approach is only an approximation for a finite periodic surface. We will verify that the error from this approximation is $\mathcal{O}$(1/`nperiods`) by comparing its result with that of a true finite periodic structure involving multiple periods in a supercell arrangement terminated with a flat surface extending into PML. (There are infinitely many ways to terminate a finite periodic structure, of course, and different choices will have slightly different errors compared to the periodic approximation.)
- print("error:, {}, {}".format(nperiods,norm_err))
- We verify that the error in `add_near2far` &mdash; defined as the $L_2$-norm of the difference of the two far-field datasets from the unit- and super-cell calculations normalized by `nperiods` &mdash; is $\mathcal{O}$(1/`nperiods`) by comparing results for three values of `nperiods`: 5, 10, and 20. The error values, which are displayed in the output in the line prefixed by `error:`, are: `0.0001195599054639075`, `5.981324591508146e-05`, and `2.989829913961854e-05`. The pairwise ratios of these errors is nearly 2 as expected (i.e., doubling `nperiods` results in halving the error).
- When these two conditions are not met as in the example below involving a small `dpad` and large `d2`, the error from the finite truncation and numerical dispersion can be large and therefore result in a significant mismatch between the far fields computed using the near-to-far field transformation versus the actual DFT fields at the same location.
