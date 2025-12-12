
# Modeling internal tides in Monterey Submarine Canyon

## Project Description
I investigate internal wave dynamics in a high-resolution regional model of Monterey Bay. My science question is

*"How well does a regional model configuration of MITgcm forced by ECCO's LLC4320, the highest-resolution global state estimate, represent internal wave   characteristics in Monterey Bay?"*

To investigate this question, I construct a model spanning Monterey Bay and its environs. In this repository, I will analyze output from my first successful model run, which ran for 10 hours on the Spartan HPC cluster, equivalent to over a day and a half in model time. This run used a relatively conservative timestep of 5 seconds, and includes hourly output of 3D temperature and velocity fields. This hourly temporal frequency should be sufficient to resolve internal tidal waves occuring at the M2 frequency (~12.42 hours), as well as near-inertial waves just under the Coriolis frequency. Note that the  namelist files 'data' and 'data.diagnostics' in this repositiory are modified from the initial run that I analyze, as I am currently running a model with a larger timestep (25 seconds), and more frequent diagnostic output (half-hourly).

To initialize the model, I downscale initial and boundary conditions from ECCO's LLC4320 (1/48 degree) simulations. I use fields from the first available timestep on the CalCoast folder in the ECCO database (https://data.nas.nasa.gov/ecco). This is September 13, 2011, at 2AM Pacific Time. For my model, I use 360 rows, 240 columns, and 88 vertical levels. This is equivalent to roughly a 350 meter horizontal resolution, on average. Note that in order to force internal tidal waves in the model and investigate their properties more reliably, one would need to force the model boundaries with time-varying output from LLC4320, or include a model for barotropic tidal currents at the boundary.

I will conduct a preliminary investigation of temperature cross-sections, vertical velocities, and kinetic energy that may indicate internal wave activity. Also, my analysis should be viewed with the caveat that the model is likely still spinning up. I use external forcing (atmospheric) conditions during February 2011 from ECCO's V4r4 global state estimate (https://ecco-group.org/products-ECCO-V4r4.htm). 

To analyze diagnostic output from the model, I create a timeseries of total kinetic energy to demonstrate what appears to be a near-inertial internal wave cycle, as well as a movies of temperature, kinetic energy, and zonal/meridional/vertical velocities from offshore to the head of the Monterey Submarine Canyon.

## Reproducing Model Results

The following steps outline how to construct the model files, configure and run the model, and assess the model results.

### Step 1: Create the Model Files
Several input files need to be created to run the model. Generate the following list of files using the notebooks indicated in paratheses:
- Model Grid (notebooks/ModelGrid.ipynb)
- Bathymetry (notebooks/Bathymetry.ipynb)
- Initial Conditions and Boundary Conditions (notebooks/InitialConditions.ipynb)
- External Forcing Conditions (notebooks/ExternalForcing.ipynb)
The model files should be placed into the  `input` directory.

### Step 2: Add files to the computing cluster
Once the input files have been created, the model files can be transferred to the computing cluster. Begin by cloning a copy of [MITgcm](https://github.com/MITgcm/MITgcm) into your scratch directory and make a folder for the configuration, .e.g.
```
mkdir MITgcm/configurations/MryRegional
```
Then, use the `scp` command to send the `code`, `input`, and `namelist` directories to your configuration directory. 

### Step 3: Compile the model
Once all of the files are on the computing cluster, the model can be compiled. Make a `build` directory in the configuration directory and run the following lines:
```
../../../tools/genmake2 -of ../../../tools/build_options/darwin_amd64_gfortran -mods ../code -mpi
make depend
make
```

### Step 4: Run the model
After the compilation is complete, run the model. Move to the run directory, link everything from `input` and `code`, and the submit the job script:
```
sbatch ms27401.slm
```

### Step 5: Analyze the Results
Analysis movies and figures can be found in notebooks/figures, and analysis notebooks themselves are in the notebooks folder.
