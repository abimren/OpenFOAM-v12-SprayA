# OpenFOAM-v12-SprayA
#### author: abdurrahman.imren @ WA, USA Jul 2025
Demonstration of a 2-D axisymmetric Sandia ECN Spray-A combustion simulation shown in ***Imren (2022)[^1]***. Given examples in this repository use custom chemistryModel, lagrangianParcel, multicomponentFluid and ODE libraries. Examples can be easily tested on a modest Windows laptop using WSL Ubuntu 24.04. Windows WSL is a convenient platform (one can install multiple Linux distributions on the same machine) and custom libraries in this repo were compiled on Ubuntu 24.04. There are several steps to complete (highly recommended) before proceeding with the Spray-A case. Here is a fresh installation streamline:
- Open a Windows terminal. Type '*wsl -l -o*' to see the list of valid Linux distributions.
- Install Ubuntu 24.04: '*wsl --install -d Ubuntu-24.04 --name  Ubuntu-24.04-LTS*'. If already installed, check the steps regarding with SuiteSparse and OpenBLAS installation (Do not attempt to reinstall on top of existing Ubuntu 24.04).    
- From Windows terminal drop-down menu located at the top bar, select Ubuntu-24.04-LTS.
- Install the binary version of OpenFOAM v12 (for a quick test) as instructed in https://openfoam.org/download/12-ubuntu/ .
- Make sure that *pitzDailySteady* tutorial case is working.
- Check if *.bashrc* file was loaded properly. Type: '*echo $FOAM_USER_APPBIN*' and '*echo $FOAM_USER_LIBBIN*'. If these directories do not exist, create using '*mkdir -p $FOAM_USER_APPBIN $FOAM_USER_LIBBIN*' command.
- Copy files in *bin* and *lib* directories of this repository to *$FOAM_USER_APPBIN* and *$FOAM_USER_LIBBIN*, respectively. Change directory to *$FOAM_USER_APPBIN* and type '_chmod 755 *_'. Repeat for the files in *$FOAM_USER_LIBBIN*. Make sure that '*which my_chemFoam*' command addresses the correct path. At this point, before testing '*my_chemFoam*' solver, first we need to install some linear algebra packages: SuiteSparse and OpenBLAS. They are going to be used in the chemistry ODE integration.
- These pre-compiled packages are available in Ubuntu. Installing available packages is more convenient and easier than compiling them. Type:

  <div align="center">
  <em>apt list --installed | grep -i libsuitesparse*</em>
  </div>
  
  If not installed, type '*sudo apt install libsuitesparse-dev*'. Check the installation location using:
  
  <div align="center">
  <em>dpkg -L libsuitesparse-dev</em>
  </div>

  command. Make sure that SuiteSparse was installed in '*/usr/lib/x86_64-linux-gnu/*' (For example, see that '*/usr/lib/x86_64-linux-gnu/libklu.so*' line exists). We will repeat similar steps for OpenBLAS installation. Type '*apt list | grep openblas**'. See that OpenBLAS was not installed by default. Type:

  <div align="center">
  <em>update-alternatives --list liblapack.so.3-x86_64-linux-gnu</em>
  </div>
  
  This command lists the available LAPACK libraries in the system:'*/usr/lib/x86_64-linux-gnu/lapack/liblapack.so.3*'. We are going to replace the default LAPACK and BLAS with those of OpenBLAS. Type:

  <div align="center">
  <em>sudo apt install libopenblas-dev</em>
  </div>
  
  After installing, list available LAPACK libraries:

  <div align="center">
  <em>'update-alternatives --list liblapack.so.3-x86_64-linux-gnu</em>
  </div>

  Notice that OpenBLAS was installed. Output:

  <div align="center">
  <em>/usr/lib/x86_64-linux-gnu/lapack/liblapack.so.3</em>

  <em>/usr/lib/x86_64-linux-gnu/openblas-pthread/liblapack.so.3</em>
  </div>

  Check that the system is using OpenBLAS package (i.e., link currently points to '*/usr/lib/x86_64-linux-gnu/openblas-pthread/liblapack.so.3*'):

  <div align="center">
  <em>update-alternatives --display liblapack.so.3-x86_64-linux-gnu</em>
  </div>

  OpenBLAS can exploit multiple threads in parallel. Make sure that we are using only one thread. Add this line to '*~/.bashrc*':

  <div align="center">
  <em>export OPENBLAS_NUM_THREADS=1</em>
  </div>

  For a very large mechanism (e.g., 7171 species C20 mechanism), one may want to test with 4-8 threads and compare with the performance of KLU sparse solver (Developers call Clark Kent LU solver). From these words, one should not interpret that the problem is being solved only by using high-performance linear algebra. There are so many important method details coded in the custom libraries and one may want to read ***Imren and Haworth (2016)[^2]*** 
     
 - After re-loading *.bashrc* (type '*source ~/.bashrc*'), change directory to *chemFoamSweep_v12*. Ten publicly available chemical mechanisms are tested. Note that these mechanisms were already examined using ***ThermoRefit[^3]*** (highly recommended - Note that OpenFOAM chemistry input looks like as of ***CHEMKIN[^4]*** but it is not exactly the same).
 - Change directory, for example, to '*654_nheptane*'. Open '*constant/chemistryProperties*' file and see that ODE solver was set to '*seulexKLU*'. Open '*system/controlDict*' file and see the '*application     my_chemFoam;*' line. '*my_chemFoam*' solver uses custom ODE solvers named: '*seulexDNS*' and '*seulexKLU*'. '*DNS*' refers to OpenBLAS dense solver. '*KLU*' refers to SuiteSparse-KLU sparse solver. Type '*my_chemFoam*' or '*./Allrun*' and hit the enter. Compare with the results given in *mylogs* and *validation* directories. If one wants to compare with the original OpenFOAM solver, '*chemFoam*' solver with '*seulex*' selection must be set. Solver performances can also be compared with commercial software and other open-source software Cantera (https://www.cantera.org/).
 - Change directory to '*sprayA_v12*'. Open '*constant/chemistryProperties*' file and see that ODE solver was set to '*seulexDNS*'. Recall that efficient dense solvers can be faster than sparse solvers for small size chemical mechanisms. Open '*system/controlDict*' file and see the '*solver mymulticomponentFluid;*' line. This is a custom library being called by '*foamRun*' solver. '*mymulticomponentFluid*' module is linked with custom *libchemistryModel.so,  liblagrangianParcel.so* and *libmyODE.so* libraries. Open '*system/decomposeParDict*' file and see the *simple* decomposer with 8 subdomains selection. If user's hardware does not have 8 physical cores, adjust accordingly. For testing purposes, try to use 8 processors. Open '*constant/cloudProperties*' and study the *injectionModels* key. See the *atomisationModel* was set to *blobInjection*. This is a custom implementation and plays crucial role to match with experimental liquid/vapor penetration. Secondary breakup model selection is *ReitzDiwakar*. Type '*Allrun-parallel*' and hit enter. OpenFOAM-v12 will sample the results using the input given in *functions* in *system* directory. Simulation (8e-3 s) should take ~9000-10000 s with 8 processors. Check plots in *postGraphs* directory. There, reference results are from previous OpenFOAM-v9 simulation using CVODE and ATOL:1e-20 and RTOL:1e-12 tolerance settings.
 - To visualize, hit '*foamToVTK*' command and use Paraview

<div align="center">
<img src="/sprayA.png" alt="Sandia Spray-A CFD RANS simulation" />
</div>

[^1]: [Imren, A.: A Detailed Error Quantification Analysis of Extrapolation-Based Stiff ODE
Solvers for Combustion CFD, Flow, Turbulence and Combustion (2022)](https://doi.org/10.1007/s10494-022-00369-z).
[^2]: [Imren, A., Haworth, D.C.: On the Merits of Extrapolation-Based Stiff ODE Solvers for Combustion CFD, Combustion and Flame, (2016)](https://doi.org/10.1016/j.combustflame.2016.09.018).
[^3]: [Imren,A.: ThermoRefit, A tool to inspect thermodynamic properties given in CHEMKIN NASA polynomial format](https://github.com/abimren/ThermoRefit)
[^4]: Kee, R.J., Rupley, F.M., , Miller, J.A.: CHEMKIN-II: A FORTRAN chemical kinetics package for the
analysis of gas-phase chemical kinetics. Technical Report SAND89-8009, Sandia National Laboratories
(1990).

