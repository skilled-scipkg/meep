# Evidence: meep-inputs-and-modeling

## Primary docs
- `doc/docs/Subpixel_Smoothing.md`
- `doc/docs/Scheme_User_Interface.md`
- `doc/docs/Materials.md`
- `doc/docs/Chunks_and_Symmetry.md`
- `doc/docs/Scheme_Tutorials/Material_Dispersion.md`
- `doc/docs/Scheme_Tutorials/Casimir_Forces.md`
- `doc/docs/Python_Tutorials/Third_Harmonic_Generation.md`
- `doc/docs/Python_Tutorials/Resonant_Modes_and_Transmission_in_a_Waveguide_Cavity.md`
- `doc/docs/Python_Tutorials/Mode_Decomposition.md`
- `doc/docs/Python_Tutorials/Material_Dispersion.md`
- `doc/docs/Python_Tutorials/Local_Density_of_States.md`
- `doc/docs/Python_Tutorials/GDSII_Import.md`

## Primary source entry points
- `skills/meep-inputs-and-modeling/references/doc_map.md`
- `src/fix_boundary_sources.cpp`
- `scheme/materials.scm`
- `src/structure_dump.cpp`
- `src/structure.cpp`
- `src/material_data.hpp`
- `src/material_data.cpp`
- `scheme/structure.cpp`
- `src/susceptibility.cpp`
- `src/multilevel-atom.cpp`
- `src/loop_in_chunks.cpp`
- `src/casimir.cpp`
- `scheme/casimir.scm`
- `src/GDSIIgeom.cpp`
- `src/meepgeom.hpp`
- `src/meepgeom.cpp`
- `src/sources.cpp`
- `src/h5fields.cpp`
- `src/fields_dump.cpp`
- `src/fields.cpp`

## Extracted headings
- Subpixel Smoothing
- pulse center frequency (from third-order polynomial fit)
- pulse frequency width
- Scheme User Interface
- lattice
- material
- susceptibility
- lorentzian-susceptibility
- drude-susceptibility
- multilevel-atom
- noisy-lorentzian-susceptibility or noisy-drude-susceptibility
- gyrotropic-lorentzian-susceptibility or gyrotropic-drude-susceptibility

## Executable command hints
- $$ \tilde{\varepsilon}^{-1} = \textbf{P}\langle\varepsilon^{-1}\rangle + \big(1-\textbf{P}\big)\langle\varepsilon\rangle^{-1} $$
- $$ \mathbf{a}
- $$
- $$ \frac{1}{2}ε|\mathbf{E}|^2 + \frac{1}{2}μ|\mathbf{H}|^2 $$
- $$σ_{ij} = E_i^*E_j + H_i^*H_j - \frac{1}{2} δ_{ij} \left( |\mathbf{E}|^2 + |\mathbf{H}|^2 \right)$$
- $$\operatorname{LDOS}_{\ell}(\vec{x}_0,\omega)=-\frac{2}{\pi}\varepsilon(\vec{x}_0)\frac{\operatorname{Re}[\hat{E}_{\ell}(\vec{x}_0,\omega)\hat{p}(\omega)^*]}{|\hat{p}(\omega)|^2}$$
- $$f(t) = \sum_n a_n e^{-i\omega_n t}$$
- $$\mathbf{D} = \varepsilon_\infty \mathbf{E} + \mathbf{P}$$
- $$\varepsilon(\omega,\mathbf{x}) = \left( 1 + \frac{i \cdot \sigma_D(\mathbf{x})}{\omega}  \right) \left[ \varepsilon_\infty(\mathbf{x})  + \sum_n \frac{\sigma_n(\mathbf{x}) \cdot \omega_n^2 }{\omega_n^2 - \omega^2 - i\omega\gamma_n} \right] ,$$
- $= \left( 1 + \frac{i \cdot \sigma_D(\mathbf{x})}{2\pi f}  \right) \left[ \varepsilon_\infty(\mathbf{x})  + \sum_n \frac{\sigma_n(\mathbf{x}) \cdot f_n^2 }{f_n^2 - f^2 - if\gamma_n/2\pi} \right] ,$
- $$\mathbf{P} = \sum_n \mathbf{P}_n$$
- $$\frac{d^2\mathbf{P}_n}{dt^2} + \gamma_n \frac{d\mathbf{P}_n}{dt} +  \omega_n^2 \mathbf{P}_n = \sigma_n(\mathbf{x}) \omega_n^2 \mathbf{E}$$

## Warnings and pitfalls
- Meep uses a [second-order accurate finite-difference scheme](https://en.wikipedia.org/wiki/Finite_difference_method#Accuracy_and_order) for discretizing [Maxwell's equations](Introduction.md#maxwells-equations). This means that the results from Meep converge to the "exact" result from the non-discretized (i.e., continuous) system quadratically with the grid resolution $\Delta x$. However, this second-order error $\mathcal{O}(\Delta x^2)$ is generally spoiled to first-order error $\mathcal{O}(\Delta x)$ if the discretization involves a *discontinuous* material boundary. Moreover, directly discretizing a discontinuity in $\varepsilon$ or $\mu$ leads to "stairstepped" interfaces that can only be varied in discrete jumps of one voxel. Meep solves both of these problems by smoothing $\varepsilon$ and $\mu$: before discretizing, discontinuities are smoothed into continuous transitions over a distance of one voxel $\Delta x$, using a second-order accurate averaging procedure summarized [below](#smoothed-permittivity-tensor-via-perturbation-theory). Subpixel smoothing, illustrated in the following schematic, enables the discretized solution to converge as quickly as possible to the exact solution as the `resolution` increases.
- 3. Objects with sharp corners or edges are associated with field singularities which introduce an unavoidable error intermediate between first and second order.
- A key feature of Meep's subpixel smoothing, particularly relevant for shape optimization (i.e., [Applied Physics Letters, Vol. 104, 091121 (2014)](https://aip.scitation.org/doi/abs/10.1063/1.4867892) ([pdf](http://ab-initio.mit.edu/~oskooi/papers/Oskooi14_tandem.pdf))), is that continuously varying the `geometry` yields continuously varying results. This is demonstrated for a ring resonator: as the radius increases, the frequency of a resonant $H_z$-polarized mode decreases. Note: unlike the example in [Tutorial/Basics/Modes of a Ring Resonator](Python_Tutorials/Basics.md#modes-of-a-ring-resonator) involving $E_z$-polarized modes where the electric fields are always continuous (i.e., parallel to the interface), this example involves discontinuous fields. Also, the ring geometry contains no sharp corners/edges which tend to produce field singularities that degrade the error. The simulation script is shown below. The inner ring radius is varied from 1.8 to 2.0 μm in gradations of 0.005 μm. The ring width is constant (1 μm). The resolution is 10 voxels/μm. The gradations are therefore well below voxel dimensions.
- To compare the convergence rate of the discretization error, the following plot shows the error in the resonant frequency (relative to the "exact" result at a resolution of 300 voxels/μm) as a function of the grid resolution for a ring geometry with a fixed inner radius of 2.0 μm. The no-smoothing results have a linear error due to the stairstepped interface discontinuities. The subpixel-smoothing results have roughly second-order convergence.
- The adaptive numerical integration used for subpixel smoothing of material functions tends to be significantly slower than the analytic approach. To speed this up at the expense of reduced accuracy, the values for its two convergence parameters `subpixel_tol` (tolerance) and `subpixel_maxeval` (maximum number of function evaluations) can be increased/lowered.
- Meep only does [subpixel averaging](Subpixel_Smoothing.md) of the *instantaneous* (i.e., infinite frequency) part of $\varepsilon$ and $\mu$. The dispersive part is not averaged at all (i.e., any frequency dependence of $\varepsilon$ and $\mu$ is ignored). This means that any discontinuous interfaces between dispersive materials will dominate the error, and you will probably get only first-order convergence, the same as if you do no subpixel averaging at all. Unfortunately, applying anisotropic subpixel smoothing to dispersive materials would result in a material with higher-degree frequency dependence, greatly complicating the implementation in the time domain.
- It is possible that the subpixel averaging may still improve the constant factor in the convergence if not the asymptotic convergence rate, if you also have a lot of interfaces between non-dispersive materials or if the dispersion is small (i.e., if $\varepsilon$ is close to $\varepsilon_{\infty}$ over your bandwidth). On the other hand, if the dispersion is large and most of your interfaces are between large-dispersion materials, then subpixel averaging may not help at all and you might as well turn it off (which may improve [stability](#why-are-the-fields-blowing-up-in-my-simulation)). Generally, the subpixel averaging will not degrade accuracy though it will affect performance. See [Issue \#1064](https://github.com/NanoComp/meep/issues/1064).
- The following convergence plot shows the frequency for the resonant mode with $H_z$ polarization and $Q$ of ~10<sup>7</sup> as a function of resolution.
- There are three important items to note. (1) The pixel grid and prism representations are each converging to a different frequency than the material function and cylinder. This is because in the limit of infinite resolution, they are *different* structures than the cylinders. (2) The material function is the same structure as the cylinder with no smoothing. In the limit of infinite resolution, the material function and cylinder converge to the same frequency. The only difference is the *rate* of convergence: the cylinder is second order (due to subpixel smoothing) whereas the material function is first order. See the convergence plot above (third figure from the top). (3) The non-interpolated pixel grid shows irregular convergence compared with the interpolated grid. This is expected because the non-interpolated grid is discontinuous but the interpolated grid is not. Also, because these are different structures the two pixel grids converge to different frequencies. To see this trend clearly requires reducing the "jumpiness" of the non-interpolated grid: the Meep resolution needs to be increased beyond 200 which is already ~3X the grid resolution.
- In general, by making a discontinuous structure continuous, via subpixel smoothing or some other form of interpolation, the convergence becomes more regular (the results change more continuously to changes in resolution or other parameters), although it does not necessarily become more accurate compared to the desired infinite-resolution structure unless the full anisotropic smoothing is performed. If the initial structure is already continuous, no additional preprocessing is necessary.
- The plot of the resonant mode frequency with resolution is shown in the figure below. There are two items to note: (1) three structures — "no smoothing", "Gaussian blur", and Sigmoid smoothed with pixel-sized smoothing width (`dr = 1/resolution`) labeled "Sigmoid boundaries (width=1/resolution)" — are converging to the same frequency. This is expected because the smoothing radius/width is going to zero as the resolution approaches infinity and thus the two smoothed structures are converging to the same discontinuous structure. The effect of the smoothing has been to make the convergence more regular compared with no smoothing. The Sigmoid-smoothed structure has the slowest convergence rate of the three. (2) The Sigmoid-smoothed structure with constant smoothing width (`dr = 0.05`) labeled "Sigmoid boundaries (width=constant)" is converging to a different frequency than the other three structures because it is a different structure.
- —For `CYLINDRICAL` simulations with |*m*| &gt; 1, compute more accurate fields near the origin $r=0$ at the expense of requiring a smaller Courant factor. Empirically, when this option is set to `true`, a Courant factor of roughly $\min[0.5, 1 / (|m| + 0.5)]$ or smaller seems to be needed. Default is `false`, in which case the $D_r$, $D_z$, and $B_r$ fields within |*m*| pixels of the origin are forced to zero, which usually ensures stability with the default Courant factor of 0.5, at the expense of slowing convergence of the fields near $r=0$.
