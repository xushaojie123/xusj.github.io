---
title: Ring-Oscillator
date: 2024-03-04 11:21:00
tags:
---
---
## 1 **sp** ————A 7-stage Ring-Oscillator
---
``` SPICE
*###############
*#    Title    #
*###############
*HSPICE GMID METHOD v2.0*
*Harrychang*

*################
*#    Option    #
*################
.option post dccap probe node print
.option post_version = 9601  

*###############
*#    Model    #    
*###############
.lib 'C:/Users/Xsj/Desktop/test/77K_model/SEC_usage.l' TT
*.lib '../SAMSUNG_28_normal/SEC_usage.l' TT

*###############
*#   SubCKt    #    
*###############
.SUBCKT C8T28SOI_LL_IVX2_P0 A Z VSS VPW VDD VNW
xMMN1 Z A VSS VPW lvtnfet l=0.03u nf=1 ngcon=1 p_la=0 plorient=1 ptwell=0 w=0.108u m=1
xMMP1 Z A VDD VNW lvtpfet l=0.03u nf=1 ngcon=1 p_la=0 plorient=1 ptwell=0 w=0.164u m=1
.ENDS


*###############
*#  Parameter  #
*###############
.temp -196
*.temp 27

.param supply_voltage = 1

*.param PARAM_VBLOW = 0 
*.param PARAM_VBHIGH = 0
.param PARAM_VBLOW = 2000m 
.param PARAM_VBHIGH = -2000m 

.ic V(n1)='supply_voltage'


*###############
*#  Circuit    #
*###############
X1 n1 n2 VSS VPW VDD VNW C8T28SOI_LL_IVX2_P0
X2 n2 n3 VSS VPW VDD VNW C8T28SOI_LL_IVX2_P0
X3 n3 n4 VSS VPW VDD VNW C8T28SOI_LL_IVX2_P0
X4 n4 n5 VSS VPW VDD VNW C8T28SOI_LL_IVX2_P0
X5 n5 n6 VSS VPW VDD VNW C8T28SOI_LL_IVX2_P0
X6 n6 n7 VSS VPW VDD VNW C8T28SOI_LL_IVX2_P0
X7 n7 n1 VSS VPW VDD VNW C8T28SOI_LL_IVX2_P0

*################
*# Voltage Sour #
*################
VGNDAN VSS 0 0

VBLOW VPW 0 'PARAM_VBLOW'
VBHIGH VNW 0 'PARAM_VBHIGH'

VDDA VDD 0 PWL 0 0 1n 'supply_voltage'


*################
*# TRAN sweep   #
*################
.TRAN 1p 30n

*################
*#    Check     #
*################

*# Overall #
.probe v(n1) v(n2) v(n3) v(n4) v(n5) v(n6) v(n7) v(VDD) i(VDDA)
.measure tran power AVG P(vdda) from=5n to=30n
.end
```
---
## 3 **output.mt0** 
---
``` output.mt0
$DATA1 SOURCE='PrimeSim HSPICE' VERSION='S-2021.09 win64' PARAM_COUNT=0
.TITLE '*###############'
 power            temper           alter#          
 -7.840e-05       -196.0000        1               
```

