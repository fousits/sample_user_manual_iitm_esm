###### sample_user_manual_iitm_esm
- Please have a look at it @prajeeshag 
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



