###### sample_user_manual_iitm_esm
 
- Set up a user manual for iitm-esm-how to get the code, how to compile and how to make runs
- Initiated by fousiyats@tropmet.res.in (08/04/2022)

#### IITM-ESM BASIC INFO (of the current version being tested for DP)


- **Atmo**       GFST126L64 (low resolution as of now)
- **Ocean**      MOM4p1 -0.5 deg
- **BGC**        Generic TOPAZ (off now, this module is not fully tested, so suggested to switch it off, see input.nml)
- **Land**       NOAH LSM
- **Sea Ice**    SIS coupled to MOM4p1

#### Number of model years completed per day~3 years (with the above config)
- Time taken to run 1 year~7hrs
- With 3 month restart frequency and with dt_ocean = 3600,dt_cpld = 3600, dt_atmos=600, deltim=600
- *Approximate time taken to run one year ~7 hrs
- *10 yrs>70hrs ~3 days (for 1 decadal ensemble)\

#### Get the code from Github
git **clone** https://github.com/prajeeshag/iitm-esm.git 
(or you could **fork** the repository from github for a start> this will be explained later in this doc)

#### _CAUTION: Never ever copy the code from one account to another, always clone it from the original repository else it is impossible to tracke the changes!!!


#### Compilation
- **STEP 1** Login node (10.12.1.31)
- Module load pbs (if not loaded)
- Module load cray-netcdf 

 - Other modules are preloaded in login node
 - modules/3.2.11.2                             
 - xpmem/2.2.2-6.0.4.1_18.1__g43b0535.ari
 - eproxy/2.0.16-6.0.4.1_3.1__g001b199.ari       
 - job/2.2.2-6.0.4.0_8.2__g3c644b5.ari
 - cce/8.6.3                                 
 - dvs/2.7_2.2.36-6.0.4.1_16.3__g4c8274a
 - craype-network-aries                          
 - alps/6.4.3-6.0.4.1_2.1__g92a2fc0.ari
 - craype/2.5.13                                
 - rca/2.2.11-6.0.4.0_13.2__g84de67a.ari
 - cray-libsci/17.09.1                          
  - atp/2.1.1
  - udreg/2.3.2-6.0.4.0_12.2__g2f9c3ee.ari      
  - perftools-base/6.5.2
  - ugni/6.0.14-6.0.4.0_14.1__ge7db4a2.ari       
  - PrgEnv-cray/6.0.4
  - pmi/5.0.12                                  
  - cray-mpich/7.6.3
  - dmapp/7.1.1-6.0.4.0_46.2__gb8abda2.ari      
  - gcc/7.2.0
  - gni-headers/5.0.11-6.0.4.0_7.2__g7136988.ari  
  - pbs/default
----
  - **STEP 2>** cd iitm-esm
  - **STEP 3>**./complile.sh  
  >executables (atm.exe, ocean.exe, aocoupler.exe, nc_combine.exe) are generated and correct path of these executables should be given in the run script>esm_submit_pratyush.pbs”
  - **Note:Executables created**
1. exec/aocoupler/aocoupler.exe
2. exec/gfs/gfs/atm.exe
3. exec/mom4p1/mom4p1/ocean.exe
4. postprocessing/nc_combine/nc_combine.exe> to combine the output after run for ocean model
5. gfs/nc_combine_gfs/nc_combine_gfs.exe >for atmospheric model

- **STEP 4>** Give the path of these executables in the runscript ( esm_submit_pratyush.pbs) 
### NAMELISTS
- Vi gfs_namelist  (atmosphere namelist)
Current_time is not taking from here, it is taken from the timestamp file
fhout=24
fhmax=85416 * (total time of the model run in hours, set for almost 10 yrs here)
igen=199
deltim=600  >delta time step
restart_interval=0,3,0,0,0,0  (year, mon, days, hours, seconds) >3 monthly output frequency now
jcap=126 >atm resolution
levs=64
ictm=1  >need to be checked and confirmed for DP 

- vi input.nml (ocean namelist)
&generic_tracer_nml
       do_generic_tracer=.false.
       do_generic_CFC=.false.                 ! no CFCs  (BGC is off now)
       do_generic_TOPAZ=.false.
       
 ##### Edits to be made in the namelists/tables before test run if you have forked/cloned the esm code from github
 ##### These are just for testing. You could set the output frequency according your problem of interest.
- For MOM:
Input.nml >make_exchange_reproduce = .false.
data_table >Comment or delete all the lines with “OCN” and the last two lines with “ATM”
Diag_table>Change the output frequency to months and replace “OUTPUT/ICE” and “OUTPUT/OCN” by “OUTPUT”
For example, change the following line
< "OUTPUT/OCN/ocean%4yr%2mo%2dy",            24,  "hours", 1, "hours", "time",24, "hours", "1801 1 1 0 0 0",
 as follows:
"OUTPUT/ocean%4yr%2mo%2dy",          1,  "months", 1, "hours", "time",1, "months", "1801 1 1 0 0 0",
Also, I have commented the topaz (BGC module) line for testing (Prajeesh Suggested)
- **For GFS**:
atm_diag_table >replace “OUTPUT/ATM/MON” by “OUTPUT” (to change where the output files are being generated)
Also, comment the following line (to write monthly outputs)
OUTPUT/ATM/DAY/atm_day%4yr%2mo%2dy%2hr", 1,  "days", 1, "hours", "time", 1, "days"

 #### RUN SUBMISSION (how to submit a run?)

Vi esm_submit_pratyush.pbs
#PBS -q ocn  (set the name of your queue here!)
***Set the path of executables (atm,ocn,aocoupler)***
- qsub esm_submit_pratyush.pbs
- qstat (to check the running status)
- Tail -f out.aprun   (to check the log files of the model run)

***The model is different from IITM-ESMv2 for the following reasons:***
- Aerosol data and land use land cover data used in IITM-ESMv2 are different 
- The resolution of IITM-ESMv2 is T62, whereas here it is T126
- IITM-ESMv2 runs with biogeochemistry in the ocean

***Rather than concentrating of how this model is different from IITM-ESMv2 let’s concentrate on what we got and what we implemented until now.
 - 1.	We got IITM-ESMv1.
 - 2.	We made all the changes required to make it radiatively balanced.
 -  3.	We implemented the new Input/Output manager for efficient handling of input/output data.






