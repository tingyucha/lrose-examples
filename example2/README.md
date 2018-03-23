This example takes ... data, converts it to Cfradial format (RadxConvert),
prints the data as text (RadxPrint), converts the data to cartesian grid
(Radx2Grid), and finally uses ncview and ncdump to examine the data.

./Radx2Grid -f output/20170408/cfrad.20170408_003432.229_to_20170408_004300.205_KARX_Surveillance_SUR.nc

output to 20170408/ncf_20170408_004300.nc


use ncdump
ncdump 20170408/ncf_20170408_004300.nc

ncview ncf_20170408_004300.nc
