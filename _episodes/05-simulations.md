---
title: "Model experiments"
teaching: 0
exercises: 0
questions:
- "How to define a climate experiment?"
- "How to run long experiments?"
objectives:
- "Choose a scenario"
- "Run a long experiment (14 months)"
- "Analyze long experiment"
keypoints:
- "climate scenario"
---

<img src="../fig/long_simulations.jpg">

*   [Introduce model experiments](#introduce-model-experiments)

# Introduce model experiments

You will work **in pairs** for this practical and you will **analyze the model outputs in pairs**.  

First choose your teammate: you will have to work together to set-up and run your experiment so make sure one of you has access to abel.  

## Experiments

List of experiments to choose:  
Run the experiment for 14 months starting on 1st of January until 1st of March the following year. For this use the same restart file you used for the short experiment.

1.  **EXPNAME=CO2**:Doubling of CO2 (change CO2 value to 800 ppm > change the name list)
2.  **EXPNAME=sea_ice**: Melt of Arctic sea ice (set sea ice fraction to zero North of 40N > change the input data set)
3.  **EXPNAME=SST**: Super El Nino (add +6 K to tropical Central and East Pacific SST from 5S to 5N, 180W to 85W > change the input data set)
4.  **EXPNAME=himalaya**: Lowering of Himalaya Mountains and Tibetan Plateau (set surface Geopotential to 0 from 25N to 40N and 70E to 100E > change the input data set)

## List of experiments

The goal is to run the experiment of your choice for 14 months starting on 1st of January until 1st of March the following year. For this use the same restart file you used for the test experiment. Each of the 4 experiments is given an experiment name:

1.  **EXPNAME=CO2**:Doubling of CO2 (change CO2 value to 800 ppm > change the name list)
2.  **EXPNAME=sea_ice**: Melt of Arctic sea ice (set sea ice fraction to zero North of 40N > change the input data set)
3.  **EXPNAME=SST**: Super El Nino (add +6 K to tropical Central and East Pacific SST from 5S to 5N, 180W to 85W > change the input data set)
4.  **EXPNAME=himalaya**: Lowering of Himalaya Mountains and Tibetan Plateau (set surface Geopotential to 0 from 25N to 40N and 70E to 100E > change the input data set)

Make sure you define an environment variable EXPNAME, every time you login <font color="red">on Abel</font>:  

    # define an environment variable for your experiment (CO2, sea_ice, SST, himalaya)
    export EXPNAME=CO2

Here is the list of tasks to perform for the experiment of your choice:  

1.  [Create a new case for your experiment](#create)
2.  [Setup experiment duration (5 days)](#duration)
3.  [Experiment namelist or dataset change](#change)

### Create a new case for your experiment

Use an appropriate name for your new experiment depending on your experiment (doubling CO2, Sea ice, etc.). Suggested **EXPNAME** are given above.  
Create a new case always involve executing create_newcase.  

<font color="red">On Abel</font>:  
`

<pre>cd $HOME/cesm/cesm1_2_2/scripts

# Adjust EXPNAME depending on your experiment (CO2, sea_ice, SST, himalaya)

export EXPNAME=CO2
#
# Simulation 2: Long simulation
#
./create_newcase -case ~/cesm_case/f2000.T31T31.$EXPNAME -res T31_T31 -compset F_2000_CAM5 -mach abel
</pre>

`  

Now you should have a new directory in $HOME/cesm_case/f2000.T31T31.$EXPNAME corresponding to your new case.  
<font color="red">On Abel</font>:  

`

<pre># Make sure EXPNAME is correctly defined!

cd ~/cesm_case/f2000.T31T31.$EXPNAME
</pre>

`  
As before we start from the control experiment:  
<font color="red">On Abel</font>:  
`

<pre>./xmlchange RUN_TYPE=hybrid
./xmlchange RUN_REFCASE=f2000.T31T31.control
./xmlchange RUN_REFDATE=0009-01-01
</pre>

`  

### Setup your new experiment duration

Before running the long simulation (14 months), you would like to check your new settings on a short experiment. You will first run 5 days; check the results and then restart the same experiment for 14 months.  
Make sure you set the duration of your experiment properly. Here we wish to run 5 days from the control experiment:  
`

<pre>./xmlchange -file env_run.xml -id STOP_N -val 5
./xmlchange -file env_run.xml -id STOP_OPTION -val ndays
</pre>

`  
Now we are ready to set-up the model configuration and build the cesm executable.  

<font color="red">On Abel</font>:  
`

<pre>./cesm_setup

# Make sure EXPNAME is set properly!

./f2000.T31T31.$EXPNAME.build

</pre>

`  

The default history file from CAM is a monthly average. We can change the output frequency with the namelist variable **nhtfrq**

*   If nhtfrq=0, the file will be a monthly average
*   If nhtfrq>0, frequency is input as number of timesteps.
*   If nhtfrq<0, frequency is input as number of hours.

For instance to change the history file from monthly average to daily average, we set the namelist variable nhtfrq = -24\. We also need to copy restart files in your running directory, etc.:  

<font color="red">On Abel</font>:  
`

<pre>cat >> user_nl_cam << EOF
nhtfrq = 0,-24
fincl2 = 'T', 'U', 'V', 'PS', 'OMEGA'
EOF
cat >> user_nl_cice << EOF
grid_file = '/work/users/$USER/inputdata/share/domains/domain.ocn.48x96_gx3v7_100114.nc'
kmt_file = '/work/users/$USER/inputdata/share/domains/domain.ocn.48x96_gx3v7_100114.nc'
EOF

# Make sure EXPNAME is set properly!      

scp login.nird.sigma2.no:/projects/NS1000K/GEO4962/outputs/runs/f2000.T31T31.control/run/f2000.T31T31.control.*.0009-01-01-00000.nc  /work/users/$USER/f2000.T31T31.$EXPNAME/run/.
scp login.nird.sigma2.no:/projects/NS1000K/GEO4962/outputs/runs/f2000.T31T31.control/run/rpointer.* /work/users/$USER/f2000.T31T31.$EXPNAME/run/.
</pre>

`  

Now depending on your experiment case, you would have either to change the namelist or to change the input dataset.

### Experiment namelist or dataset change

*   [Doubling CO2](CO2/index.html)
*   [Melt of Artic sea ice](sea_ice/index.html)
*   [Super El Nino](SST/index.html)
*   [Lowering Himalaya mountains](himalaya_tibetan/index.html)

# Running long experiments (14 months)

As for the previous exercice, you will work **in pairs** for this practical and you will **analyze the model outputs in pairs**.  
You will be using your previous experiment ~/cesm_case/f2000.T31T31.$EXPNAME (EXPNAME should be set depending on your experiment!) and run 14 months.  

### Set a new duration for your experiment

Make sure you set the duration of your experiment properly. Here we wish to run 14 months from the control restart experiment but as it is a long run, we rather split it into chuncks of 1 month:  
<font color="red">On Abel</font>:  
`

<pre># Set EXPNAME properly

cd ~/cesm_case/f2000.T31T31.$EXPNAME

./xmlchange -file env_run.xml -id STOP_N -val 1
./xmlchange -file env_run.xml -id STOP_OPTION -val nmonths
</pre>

`To perform a 14 months experiment, we would need to repeat this one month experiment 14 times. For this we set a CESM option called RESUBMIT:`

<pre>./xmlchange -file env_run.xml -id RESUBMIT -val 14
</pre>

`  
By setting this option, CAM5 will be running one month of simulation (once submitted) and automatically resubmit the next 13 months.  

### Adjust parameters for your long batch submission

Before submitting your experiment (f2000.T31T31.$EXPNAME.submit), you need to adjust [wall clock time](https://en.wikipedia.org/wiki/Wall-clock_time) and adjust CPU resources.  
As each chunk will run 1 month of your experiment, you need to assess the duration of one month simulation. For more information on how to update the wall clock time for your CAM5 simulation, go [here](wallclock.html).  

<font color="red">On Abel</font>:  
`

<pre>cd ~/cesm_case/f2000.T31T31.$EXPNAME

./f2000.T31T31.$EXPNAME.submit
</pre>

`  
Regularly check your experiment (and associated generated output files) and once it is fully done, [store your model outputs on norStore](norstore.html).

# Store model outputs on norStore

First make sure your run was successful and check all the necessary output files were generated.  
To post-process and visualize your model outputs, it is VERY IMPORTANT you move them from Abel to norStore. Remember that all model outputs are generated in a semi-temporary directory and all your files will be removed after a few weeks!  
If you haven't set-up your [SSH keys](http://www.mn.uio.no/geo/english/services/it/help/using-linux/ssh-tips-and-tricks.html), the next commands (ssh and [rsync](http://www.tecmint.com/rsync-local-remote-file-synchronization-commands/)) will require you to enter your Unix password.  
Make sure you define EXPNAME properly (it depends on your experiment):  
<font color="red">On Abel</font>:`

<pre># If you are running CO2 experiment (otherwise adjust: sea_ice, SST, rocky)
export EXPNAME=CO2
</pre>

`Then copy files from abel to norStore project area:  
<font color="red">On Abel</font>:`

<pre>ssh login.nird.sigma2.no 'mkdir -p /projects/NS1000K/GEO4962/outputs/$USER/runs'
ssh login.nird.sigma2.no 'mkdir -p /projects/NS1000K/GEO4962/outputs/$USER/archive'

rsync -avz /work/users/$USER/f2000.T31T31.$EXPNAME $USER@login.nird.sigma2.no:/projects/NS1000K/GEO4962/outputs/$USER/runs/.

rsync -avz /work/users/$USER/archive/f2000.T31T31.$EXPNAME $USER@login.nird.sigma2.no:/projects/NS1000K/GEO4962/outputs/$USER/archive/.
</pre>

`  
Once the previous commands are successful, you are ready to [post-process and visualize](../../results.html) your data on login.nird.sigma2.no  

However, as your simulation is stored on the norStore project area, you can now [archive your experiment](archive.html) on the norStore archive (long-term archive i.e. several years).

# Post processing and visualization

{% include links.md %}
