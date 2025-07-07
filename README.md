# OpenFOAM-v12-SprayA
#### author: abdurrahman.imren @ WA, USA Jul 2025
Demonstration of a 2-D axisymmetric Spray-A combustion simulation shown in ***Imren (2022)[^1]***. Given examples in this repository use custom chemistryModel, lagrangianParcel, multicomponentFluid and ODE libraries. Examples can be easily tested on a modest Windows laptop using WSL Ubuntu 24.04. There are several steps to be done (highly recommended) before proceeding with the Spray-A case. A tested installation streamline (Windows WSL is a convenient platform - one can install multiple Linux distributions on the same machine):
- Open a Windows terminal. Type '*wsl -l -o*' to see the list of valid Linux distributions
- Install Ubuntu 24.04: '*wsl --install -d Ubuntu-24.04 --name  Ubuntu-24.04-LTS*'
- From Windows terminal drop-down menu located at the top bar, select Ubuntu-24.04-LTS
- Install the binary version of OpenFOAM v12 (for a quick test) as instructed in https://openfoam.org/download/12-ubuntu/
- Make sure that *pitzDailySteady* tutorial case worked
- Check *.bashrc* was loaded properly. Type: '*echo $FOAM_USER_APPBIN*' and '*echo $FOAM_USER_LIBBIN*'. If these directories do not exists, create using: '*mkdir -p $FOAM_USER_APPBIN $FOAM_USER_LIBBIN*'
- Copy files in *bin* and *lib* directories of this repository to *$FOAM_USER_APPBIN* and *$FOAM_USER_LIBBIN*, respectively. '*which my_chemFoam*' command should address the correct path. At this point, before testing '*my_chemFoam*' solver, first we need to install some linear algebra packages for the chemistry ODE integration: SuiteSparse and OpenBLAS.
- These pre-compiled linear algebra packages are available in Ubuntu to download and install. Type:

  <div align="center">
  <em>apt list --installed | grep -i libsuitesparse*</em>
  </div>
  
  If not installed, type '*sudo apt install libsuitesparse-dev*'. Check the installation location using:
  
  <div align="center">
  <em>dpkg -L libsuitesparse-dev</em>
  </div>

  command. Make sure that SuiteSparse was installed in '*/usr/lib/x86_64-linux-gnu/*' (For example, see that '*/usr/lib/x86_64-linux-gnu/libklu.so*' exists). We will repeat the same steps for OpenBLAS installation. Type '*apt list | grep openblas**'. See that OpenBLAS was not installed by default. Type:

  <div align="center">
  <em>update-alternatives --list liblapack.so.3-x86_64-linux-gnu</em>
  </div>
  
  This command shows the available LAPACK library in the system:'*/usr/lib/x86_64-linux-gnu/lapack/liblapack.so.3*'. We are going to replace default LAPACK and BLAS with those of OpenBLAS. Type:

  <div align="center">
  <em>sudo apt install libopenblas-dev</em>
  </div>
  
  After installing, list available LAPACK libraries:

  <div align="center">
  <em>'update-alternatives --list liblapack.so.3-x86_64-linux-gnu</em>
  </div>

  We see that OpenBLAS was installed to:

  <div align="center">
  <em>/usr/lib/x86_64-linux-gnu/lapack/liblapack.so.3</em>

  <em>/usr/lib/x86_64-linux-gnu/openblas-pthread/liblapack.so.3</em>
  </div>

  Check that system is using OpenBLAS LAPACK (i.e., link currently points to /usr/lib/x86_64-linux-gnu/openblas-pthread/liblapack.so.3):

  <div align="center">
  <em>update-alternatives --display liblapack.so.3-x86_64-linux-gnu</em>
  </div>

  OpenBLAS can exploit multiple threads in parallel. Make sure that we are using only one thread. Add this line to '*~/.bashrc*':

  <div align="center">
  <em>export OPENBLAS_NUM_THREADS=1</em>
  </div>

  For a very large mechanism (e.g., 7171 species C20 mechanism), one may want to test with 4-8 threads and compare with the performance of KLU sparse solver (Authors call Clark Kent LU solver). From these words, one should not interpret that the problem is being solved only by using high-performance linear algebra. There are so many important method details coded in the custom libraries and one may want to read ***Imren and Haworth (2016)[^2]*** 
     
 - After loading *.bashrc* (type '*source ~/.bashrc*'), change directory to *chemFoamSweep_v12*. 10 publicly available chemical mechanisms is tested. Note that these mechanisms were already examined using ***ThermoRefit[^3]*** (highly recommended).
 - Change directory, for example, to '*654_nheptane*'. Open '*constant/chemistryProperties*' file and see that ODE solver was set to '*seulexKLU*'. Open '*system/controlDict*' file and see the '*application     my_chemFoam;*' line. '*my_chemFoam*' solver uses custom ODE solvers named: '*seulexDNS*' and '*seulexKLU*'. '*DNS*' refers to OpenBLAS dense solver. '*KLU*' refers to SuiteSparse-KLU sparse solver. Type '*my_chemFoam*' or '*./Allrun*'and hit the enter. Compare with the results given in *logs* and *validation* directories. If one wants to compare with the original OpenFOAM solver, '*chemFoam*' solver with '*seulex*' selection must be used.
 -     


[^1]: [Imren, A.: A Detailed Error Quantification Analysis of Extrapolation-Based Stiff ODE
Solvers for Combustion CFD, Flow, Turbulence and Combustion (2022)](https://doi.org/10.1007/s10494-022-00369-z).
[^2]: [Imren, A., Haworth, D.C.: On the Merits of Extrapolation-Based Stiff ODE Solvers for Combustion CFD, Combustion and Flame, (2016)](https://doi.org/10.1016/j.combustflame.2016.09.018).
[^3]: [Imren,A.: ThermoRefit, A tool to inspect thermodynamic properties given in CHEMKIN NASA polynomial format](https://github.com/abimren/ThermoRefit)
[^4]: Kee, R.J., Rupley, F.M., , Miller, J.A.: CHEMKIN-II: A FORTRAN chemical kinetics package for the
analysis of gas-phase chemical kinetics. Technical Report SAND89-8009, Sandia National Laboratories
(1990).

