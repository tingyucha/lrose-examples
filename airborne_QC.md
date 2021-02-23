# Airborne-Radar-QC Tutorial

## Prerequisites

1. Install the navigation correction package from https://github.com/mmbell/Airborne-Radar-QC


2. Airborne radar files with cfradial format

Select a ~10-minute period during your flight where the aircraft is flying in a straight line and at a constant altitude. Ideally, this 10-minute period is (1) in clear weather, where the ground shows up clearly in the reflectivity field and is not masked by other echoes; and (2) over land, where we know that the ground isn’t moving. But, some light rain and/or while the aircraft is over the ocean is still acceptable. In hurricane reconnaissance flights, outbound or inbound legs are typically good. You will need to know the exact number of sweep files in that 10-minute period.

3. Set up the navigation correction directory

## Initial Navigation Correction

1. Copy the following variables to new fields
```terminal 
copy VE to VEL
copy DZ to DBZ
copy SPEC_WDT to WIDTH
copy WIDTH to SW
```

2. Create a field called NCP and assign a value of 1 to NCP
```terminal 
copy DBZ to NCP
assign-value 1 to NCP
```

Make sure to apply the corrections to all sweep files by clicking on **First File** and **Last File**, and also to both the fore sweeps and the aft sweeps.

## Main Navigation Correction

1. Create a list file of all the generated cfradial files called **filelist**
```terminal
ls * > filelist
```

2. Insert the number of cfradial files that you have in the top line in the **filelist** file
```terminal
vi filelist
```
[display an example figure here]

3. Close filelist and run
```terminal
./navcorr/readnetcdf_DBZ_VR	filelist
```
