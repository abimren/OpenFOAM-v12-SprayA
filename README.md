# OpenFOAM-v12-SprayA
#### author: abdurrahman.imren @ WA, USA Jul 2025
Demonstration of a 2-D axisymmetric Spray-A combustion simulation shown in ***Imren (2022)[^1]***. Example uses custom chemistryModel, lagrangianParcel, multicomponentFluid and ODE libraries. Model can be easily tested on a modest Windows laptop using WSL Ubuntu 24.04. There are several steps to be done before proceeding Spray-A case. A tested installation streamline:
- Open a Windows terminal. Type 'wsl -l -o' to see the list of valid Linux distributions
- Install Ubuntu 24.04: 'wsl --install -d Ubuntu-24.04 --name  Ubuntu-24.04-LTS'
- From Windows terminal drop down menu located at top bar, select Ubuntu-24.04-LTS 
- Install and test the binary version of OpenFOAM v12 as instructed in https://openfoam.org/download/12-ubuntu/
- Make sure that pitzDailySteady tutorial case worked
- 'echo $FOAM_USER_APPBIN' and 'echo $FOAM_USER_LIBBIN'. If not exists, create these directories: 'mkdir -p $FOAM_USER_APPBIN $FOAM_USER_LIBBIN'
- Copy files in *bin* and *lib* directories of this repository to $FOAM_USER_APPBIN and $FOAM_USER_LIBBIN, respectively. 'which my_chemFoam' command should show the correct path
-     
  
***Imren and Haworth (2016)[^2]***

[^1]: [Imren, A.: A Detailed Error Quantification Analysis of Extrapolation-Based Stiff ODE
Solvers for Combustion CFD, Flow, Turbulence and Combustion (2022)](https://doi.org/10.1007/s10494-022-00369-z).
[^2]: [Imren, A., Haworth, D.C.: On the Merits of Extrapolation-Based Stiff ODE Solvers for Combustion CFD, Combustion and Flame, (2016)](https://doi.org/10.1016/j.combustflame.2016.09.018).
[^3]: Kee, R.J., Rupley, F.M., , Miller, J.A.: CHEMKIN-II: A FORTRAN chemical kinetics package for the
analysis of gas-phase chemical kinetics. Technical Report SAND89-8009, Sandia National Laboratories
(1990).

