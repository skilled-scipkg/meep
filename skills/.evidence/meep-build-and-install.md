# Evidence: meep-build-and-install

## Primary docs
- `doc/docs/Installation.md`
- `doc/docs/FAQ.md`
- `doc/docs/Build_From_Source.md`
- `doc/docs/Scheme_Tutorials/Local_Density_of_States.md`
- `doc/docs/Scheme_Tutorials/Third_Harmonic_Generation.md`
- `doc/docs/Scheme_Tutorials/Resonant_Modes_and_Transmission_in_a_Waveguide_Cavity.md`
- `doc/docs/Scheme_Tutorials/Optical_Forces.md`
- `doc/docs/Scheme_Tutorials/Cylindrical_Coordinates.md`
- `doc/docs/Download.md`
- `doc/docs/Scheme_Tutorials/Multilevel_Atomic_Susceptibility.md`

## Primary source entry points
- `skills/meep-build-and-install/references/doc_map.md`
- `src/Makefile.am`
- `scheme/Makefile.am`
- `libpympb/Makefile.am`
- `src/support/Makefile.am`
- `src/susceptibility.cpp`
- `src/multilevel-atom.cpp`
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

## Extracted headings
- Installation
- Official Releases
- Create an environment with the serial version of pymeep 1.8.0
- Create an environment with the parallel version of pymeep 1.9.0
- Version Number
- Non-Networked Systems
- FAQ
- What is Meep?
- Who are the developers of Meep?
- Where can I ask questions regarding Meep?
- How can I contribute to the Meep project?
- Is there a technical reference for Meep?

## Executable command hints
- mpirun -np 4 python <script_name>.py
- ./configure CPPFLAGS="-I$(brew --prefix)/include" LDFLAGS="-L$(brew --prefix)/lib" PYTHON=python3 && make && sudo make install
- ./configure --prefix=$HOME/install
- ./configure LDFLAGS="-L$HOME/install/lib" CPPFLAGS="-I$HOME/install/include"   ...other flags...
- ./configure LDFLAGS="-L$HOME/install/lib -Wl,-rpath,$HOME/install/lib" ...other flags...
- ./configure
- ./build-meep.sh
- ./autogen.sh
- ./configure --enable-optimizations
- ./configure --enable-parallel --enable-shared --prefix=/usr/local CC=/usr/local/bin/mpicc CXX=/usr/local/bin/mpic++
- $$\operatorname{LDOS}_{\ell}(\vec{x}_0,\omega)=-\frac{2}{\pi}\varepsilon(\vec{x}_0)\frac{\operatorname{Re}[\hat{E}_{\ell}(\vec{x}_0,\omega)\hat{p}(\omega)^*]}{|\hat{p}(\omega)|^2}$$
- $$\operatorname{resonant\ LDOS} \approx \frac{2}{\pi\omega^{(n)}} \frac{Q^{(n)}}{V^{(n)}}$$

## Warnings and pitfalls
- **Note:** There is currently an issue with openblas 0.3.5 that causes segmentation faults on newer Skylake X-series cpus. If `import meep` results in an "illegal instruction" error, downgrade openblas to version `0.3.4` as follows:
- Warning: The `pymeep` package is built to work with OpenBLAS, which means numpy should also use OpenBLAS. Since the default numpy is built with MKL, installing other packages into the environment may cause conda to switch to an MKL-based numpy. This can cause segmentation faults when calling MPB. To work around this, you can make sure the `no-mkl` conda package is installed, make sure you're getting packages from the `conda-forge` channel (they use OpenBLAS for everything), or as a last resort, run `import meep` before importing any other library that is linked to MKL. When installing additional packages into the `meep` environment, you should always try to install using the `-c conda-forge` flag. `conda` can occasionally be too eager in updating packages to new versions which can leave the environment unstable. If running `conda install -c conda-forge <some-package>` attempts to replace `conda-forge` packages with equivalent versions from the `defaults` channel, you can force it to only use channels you specify (i.e., arguments to the `-c` flag) with the `--override-channels` flag.
- There are two possible explanations: (1) the simulation run time may be too short or the resolution may be too low and thus your results have not sufficiently [converged](#checking-convergence), or (2) you may be using a higher-dimensional cell with multiple periods (a supercell) which introduces unwanted additional modes due to band folding. One indication that band folding is present is that small changes in the resolution produce large and unexpected changes in the frequency spectra. Modeling flat/planar structures typically requires a 1d cell and periodic structures a single unit cell in 2d/3d. For more details, see Section 4.6 ("Sources in Supercells") in [Chapter 4](http://arxiv.org/abs/arXiv:1301.5366) ("Electromagnetic Wave Source Conditions") of [Advances in FDTD Computational Electrodynamics: Photonics and Nanotechnology](https://www.amazon.com/Advances-FDTD-Computational-Electrodynamics-Nanotechnology/dp/1608071707). Note that a 1d cell must be along the $z$ direction with only the $E_x$ and $H_y$ field components permitted.
- A continuous-wave source ([ContinuousSource](Python_User_Interface.md#continuoussource)) produces fields which are not integrable: their Fourier transform will not converge as the run time of the simulation is increased because the source never terminates. The Fourier-transformed fields are therefore arbitrarily defined by the run time. This [windowing](https://en.wikipedia.org/wiki/Window_function) does different things to the normalization and scattering runs because the spectra are different in the two cases. In contrast, a pulsed source ([GaussianSource](Python_User_Interface.md#gaussiansource)) produces fields which are [L2](https://en.wikipedia.org/wiki/Norm_(mathematics)#Euclidean_norm)-integrable: their Fourier transform is well defined and convergent as long as the run time is sufficiently large and the [fields have decayed away](#checking-convergence). Note that the amplitude of the Fourier transform grows linearly with time; the Poynting flux, which is proportional to the amplitude squared, grows quadratically.
- ### Checking convergence
- Meep's [subpixel smoothing](Subpixel_Smoothing.md) often improves the rate of convergence and makes convergence a smoother function of resolution. However, unlike the built-in geometric objects (e.g., `Sphere`, `Cylinder`, `Block`, etc.), subpixel smoothing does not occur for [dispersive materials](Subpixel_Smoothing.md#what-about-dispersive-materials) or [user-defined material functions](#why-does-subpixel-averaging-take-so-long) $\varepsilon(\vec{r})$.
- For flux calculations involving pulsed (i.e., Gaussian) sources, it is important to run the simulation long enough to ensure that all the transient fields have sufficiently decayed away (i.e., due to absorption by the PMLs, etc). Terminating the simulation prematurely will result in the Fourier-transformed fields, which are being accumulated during the time stepping (as explained in [Introduction](Introduction.md#transmittancereflectance-spectra)), to not be fully converged. Another side effect of not running the simulation long enough is that the Fourier spectra are smoothed out, causing sharp peaks to broaden into adjacent frequency bins (sometimes known as [spectral leakage](https://en.wikipedia.org/wiki/Spectral_leakage)) and hence limiting the spectral resolution (also known as the [Fourier uncertainty principle](https://en.wikipedia.org/wiki/Uncertainty_principle#Signal_processing)) — in general, the minimum runtime should be the inverse of the desired frequency resolution. (Conversely, running for long after the time-domain fields have decayed away has a *negligible effect* on the Fourier-transformed fields: the [discrete-time Fourier transform](https://en.wikipedia.org/wiki/Discrete-time_Fourier_transform) is just a sum, and adding exponentially small values to a sum will not significantly change the result.) Convergence of the fields is typically achieved by lowering the `decay_by` parameter in the `stop_when_fields_decayed` [run function](Python_User_Interface.md#run-functions).  Alternatively, you can explicitly set the run time to some numeric value that you repeatedly double, instead of using the field decay.  Sometimes it is also informative to double the `cutoff` parameter of sources to increase their smoothness (reducing the amplitude of long-lived high-frequency modes).
- There are five possible explanations: (1) the normalization and the scattering runs are not comparable because e.g., the sources or monitors are not in the same position within the structure, (2) the [run time is not long enough](#checking-convergence) and hence all of the flux is not being collected in either or both runs, (3) the flux is being computed at a frequency which is too far away from the center of the source bandwidth; in such cases the flux values are too small and may be dominated by rounding errors, (4) the source or monitor is positioned too close to the scatterer which [modifies the local density of states (LDOS)](#how-does-the-current-amplitude-relate-to-the-resulting-field-amplitude); for example, a source emits more power near a band edge and less power within a bandgap than the same source surrounded by many wavelengths of vacuum, or (5) in the normalization run, the monitor is positioned too close to the source and is capturing unwanted (e.g., radiating) modes.
- Numerical dispersion can be analyzed and quantified analytically for a homogeneous medium. For details, see e.g., Chapter 4 ("Numerical Dispersion and Stability") of [Computational Electrodynamics: The Finite Difference Time-Domain Method (3rd edition)](https://www.amazon.com/Computational-Electrodynamics-Finite-Difference-Time-Domain-Method/dp/1580538320). However, in practice numerical dispersion is rarely the dominant source of error in FDTD calculations which almost always involve material inhomogeneities that give rise to much larger errors. Similar to other errors associated with the finite grid resolution, numerical dispersion decreases with resolution, so you can deal with it by increasing the resolution until convergence is obtained to the desired accuracy. In particular, the errors from numerical dispersion vary *quadratically* with resolution (in the ordinary center-difference FDTD scheme). On the other hand, the errors introduced by discretization of material interfaces go *linearly* with the resolution, so they are almost always dominant. Meep can partially correct for these errors using [subpixel averaging](Subpixel_Smoothing.md).
- For those installing Meep on a supercomputer, a note of caution: most supercomputers have multiple compilers installed, and different versions of libraries compiled with different compilers. Meep is written in C++, and it is almost impossible to mix C++ code compiled by different compilers &mdash; pick one set of compilers by one vendor and stick with it consistently.
- First, let's review some important information about installing software on Unix systems, especially in regards to installing software in non-standard locations. None of these issues are specific to Meep, but they've caused a lot of confusion among users.
- It is often important to be consistent about which compiler you employ. This is especially true for C++ software. To specify a particular C compiler `foo`, configure with `./configure CC=foo`; to specify a particular C++ compiler `foo++`, configure with `./configure CXX=foo++`; to specify a particular Fortran compiler `foo90`, configure with `./configure F77=foo90`.
