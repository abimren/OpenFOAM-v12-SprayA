# OpenFOAM-v12-SprayA
#### author: abdurrahman.imren @ WA, USA Jul 2025
Demonstration of a 2-D axisymmetric Spray-A combustion simulation shown in ***Imren (2022)[^1]***. Here given examples use custom chemistryModel, lagrangianParcel, multicomponentFluid and ODE libraries. Model can be easily tested on a modest Windows laptop using WSL Ubuntu 24.04. There are several steps to be done (highly recommended) before proceeding with the Spray-A case. A tested installation streamline:
- Open a Windows terminal. Type '*wsl -l -o*' to see the list of valid Linux distributions
- Install Ubuntu 24.04: '*wsl --install -d Ubuntu-24.04 --name  Ubuntu-24.04-LTS*'
- From Windows terminal drop down menu located at the top bar, select Ubuntu-24.04-LTS
- Install the binary version of OpenFOAM v12 (for a quick test) as instructed in https://openfoam.org/download/12-ubuntu/
- Make sure that *pitzDailySteady* tutorial case worked
- Check *.bashrc* was loaded properly. Type: '*echo $FOAM_USER_APPBIN*' and '*echo $FOAM_USER_LIBBIN*'. If not exists, create these directories: '*mkdir -p $FOAM_USER_APPBIN $FOAM_USER_LIBBIN*'
- Copy files in *bin* and *lib* directories of this repository to *$FOAM_USER_APPBIN* and *$FOAM_USER_LIBBIN*, respectively. '*which my_chemFoam*' command should show the correct path. At this point, first we will update the linear algebra packages
- We are going to use pre-compiled linear algebra packages available in Ubuntu for the chemistry ODE integration: SuiteSparse and OpenBLAS. Type:

  <div align="center">
  <em>apt list --installed | grep -i libsuitesparse*</em>
  </div>
  
  If not installed, type '*sudo apt install libsuitesparse-dev*'. Check the installation location using:
  
  <div align="center">
  <em>dpkg -L libsuitesparse-dev</em>
  </div>

  command. Make sure that SuiteSparse was installed in '*/usr/lib/x86_64-linux-gnu/*' (For example, see that '*/usr/lib/x86_64-linux-gnu/libklu.so*' exists). We will repeat the same steps for OpenBLAS installation. Type '*apt list | grep openblas**'. See that OpenBLAS was not shipped by default. Type:

  <div align="center">
  <em>update-alternatives --list liblapack.so.3-x86_64-linux-gnu</em>
  </div>
  
  '*/usr/lib/x86_64-linux-gnu/lapack/liblapack.so.3*'. We are going to replace this system LAPACK and BLAS with OpenBLAS. Type:

  <div align="center">
  <em>sudo apt install libopenblas-dev</em>
  </div>
  
  After installing, check available LAPACK libraries:

  'update-alternatives --list liblapack.so.3-x86_64-linux-gnu
/usr/lib/x86_64-linux-gnu/lapack/liblapack.so.3
/usr/lib/x86_64-linux-gnu/openblas-pthread/liblapack.so.3
  
-     
  
***Imren and Haworth (2016)[^2]***

[^1]: [Imren, A.: A Detailed Error Quantification Analysis of Extrapolation-Based Stiff ODE
Solvers for Combustion CFD, Flow, Turbulence and Combustion (2022)](https://doi.org/10.1007/s10494-022-00369-z).
[^2]: [Imren, A., Haworth, D.C.: On the Merits of Extrapolation-Based Stiff ODE Solvers for Combustion CFD, Combustion and Flame, (2016)](https://doi.org/10.1016/j.combustflame.2016.09.018).
[^3]: Kee, R.J., Rupley, F.M., , Miller, J.A.: CHEMKIN-II: A FORTRAN chemical kinetics package for the
analysis of gas-phase chemical kinetics. Technical Report SAND89-8009, Sandia National Laboratories
(1990).

