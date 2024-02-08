---
title: Ring-Oscillator
date: 2024-02-09 00:16:38
tags:
---
---
## 1 **sp** ————A 7-stage Ring-Oscillator
---
``` HSPICE
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
.end```

---
## 2 **output.lis** 
---
``` output.lis
 ****** PrimeSim HSPICE -- S-2021.09 win64 (Aug 25 2021 7230000) ******
  Copyright (c) 1986 - 2024 by Synopsys, Inc. All Rights Reserved.
  This software and the associated documentation are proprietary
  to Synopsys, Inc. This software may only be used in accordance
  with the terms and conditions of a written license agreement with
  Synopsys, Inc. All other use, reproduction, or distribution of
  this software is strictly prohibited.
  Input File: C:\Users\Xsj\Desktop\test\inv_osc.sp                              
  Command line options: C:\synopsys\Hspice_S-2021.09\WIN64\hspice.com -i C:\Users\Xsj\Desktop\test\inv_osc.sp -o C:\Users\Xsj\Desktop\test\output
  Start time: Fri Feb  9 00:16:23 2024
  lic:  
  lic: FLEXlm: SDK_12.9.6 
  lic: USER:   Xsj                  HOSTNAME: DESKTOP-xsj 
  lic: HOSTID: 00ff5de8477c         PID:      6516 
  lic: Using FLEXlm license file: 
  lic: C:\synopsys\synopsys_lic.dat 
  lic: Checkout 1 hspice 
  lic: License/Maintenance for hspice will expire on 30-dec-2030/2030.12 
  lic: 1(in_use)/99(total) FLOATING license(s) on SERVER C:\synopsys\synopsys_li
 c.dat 
  lic:   
  **warning** (C:\Users\Xsj\Desktop\test\inv_osc.sp:10) Unknown option print. Check the option name again.
1****** PrimeSim HSPICE -- S-2021.09 win64 (Aug 25 2021 7230000) ******
 ******  
 *###############

 ****** circuit name directory
 circuit number to circuit name directory
   number circuitname                     definition         multiplier
        0 main circuit
        1 x1.                             c8t28soi_ll_ivx2_p0
                                                                 1.00 
        2 x2.                             c8t28soi_ll_ivx2_p0
                                                                 1.00 
        3 x3.                             c8t28soi_ll_ivx2_p0
                                                                 1.00 
        4 x4.                             c8t28soi_ll_ivx2_p0
                                                                 1.00 
        5 x5.                             c8t28soi_ll_ivx2_p0
                                                                 1.00 
        6 x6.                             c8t28soi_ll_ivx2_p0
                                                                 1.00 
        7 x7.                             c8t28soi_ll_ivx2_p0
                                                                 1.00 
        8 x1.xmmn1.                       lvtnfet            1.00 
        9 x1.xmmp1.                       lvtpfet            1.00 
       10 x2.xmmn1.                       lvtnfet            1.00 
       11 x2.xmmp1.                       lvtpfet            1.00 
       12 x3.xmmn1.                       lvtnfet            1.00 
       13 x3.xmmp1.                       lvtpfet            1.00 
       14 x4.xmmn1.                       lvtnfet            1.00 
       15 x4.xmmp1.                       lvtpfet            1.00 
       16 x5.xmmn1.                       lvtnfet            1.00 
       17 x5.xmmp1.                       lvtpfet            1.00 
       18 x6.xmmn1.                       lvtnfet            1.00 
       19 x6.xmmp1.                       lvtpfet            1.00 
       20 x7.xmmn1.                       lvtnfet            1.00 
       21 x7.xmmp1.                       lvtpfet            1.00 
 ******  
 *###############

 ****** mos   model parameters tnom=  25.000 temp=  25.000 ******
 Model file: C:\Users\Xsj\Desktop\test\77K_model\LVT.lib
 Model line:          484
 
Model Name: lvtnfet 

 UTSOI Model(Level76)

 UTSOI VERSION    2.1 

  SWSCALE =      1 
  SWCLIPCHK =      0 
  SWSUBDEP =      1 
  SWIGATE =      1 
  SWGIDL =      1 
  SWSHE =      0 
  SWIGN =      0 
  SWJUNASYM =      0 
  QMC =    1.3 
  TYPE = 1 
  TR =   -196 
  TMAX =    150 
 Local Model 

  TOXE =  2e-09 
  TSI =  1e-08 
  XGE =      0 
  TBOX =  1e-07 
  NSUB =  3e+18 
  CT =      0 
  TOXP =  2e-09 
  NOV =  1e+20 
  NOVD =  1e+20 
  VFB =      0 
  VFBB =      0 
  STVFB =      0 
  CICF =      1 
  CIC =      1 
  PSCE =      0 
  PSCEB =      1 
  CF =      0 
  CFB =      1 
  STCF =      0 
  CFD =    0.2 
  BETN =   0.05 
  BETNB =      1 
  STBET =    1.5 
  CS =      0 
  STCS =      0 
  THECS =    1.5 
  STTHECS =      0 
  CSTHR =      2 
  MUE =      0 
  STMUE =      0 
  THEMU =    1.5 
  STTHEMU =      0 
  XCOR =      0 
  STXCOR =      0 
  FETA =      1 
  RS =     30 
  STRS =      0 
  RSG =      0 
  THERSG =      2 
  THESAT =      0 
  STTHESAT =   -0.1 
  THESATG =      0 
  THESATB =      0 
  AX =      8 
  ALP =      0 
  ALP1 =      0 
  VP =   0.05 
  GCO =      0 
  IGINV =      0 
  IGOVINV =      0 
  IGOVINVD =      0 
  IGOVACC =      0 
  IGOVACCD =      0 
  STIG =      0 
  GC2CH =  0.375 
  GC3CH =  0.063 
  GC2OV =  0.375 
  GC3OV =  0.063 
  CHIB =    3.1 
  AGIDL =      0 
  AGIDLD =      0 
  BGIDL =     41 
  BGIDLD =     41 
  STBGIDL =      0 
  STBGIDLD =      0 
  CGIDL =      0 
  CGIDLD =      0 
  AREAQ =  1e-12 
  CGBOV =      0 
  COV =      0 
  COVD =      0 
  CFR =      0 
  CFRD =      0 
  CSD = 1.04e-18 
  CSDBP =      0 
  RTH =  1e+04 
  STRTH =      0 
  CTH =  1e-11 
  FNT =      1 
  NFA =  8e+22 
  NFB =  3e+07 
  NFC =      0 
  NFE =      0 
  NFEB =      0 
  EF =      1 
  PSCEDL =      0 
  PNCE =      0 
  CFDL =      0 
  GC2OVINV =  0.375 
  GC3OVINV =  0.063 
  GC2OVACC =  0.375 
  GC3OVACC =  0.063 
  NIGINV =      0 
  COVDL =      0 
  DVFBOV =      0 
 Global Model 

  LVARO =  4e-09 
  LVARL =      0 
  LVARW =      0 
  LAP = -1.5e-09 
  WVARO =  2e-08 
  WVARL =      0 
  WVARW =      0 
  WOT =      0 
  DLQ = 3.5e-08 
  DWQ = -1.7e-08 
  TOXEO = 1.085e-09 
  TSIO = 6.7e-09 
  XGEO =   0.36 
  TBOXO = 2.6e-08 
  NSUBO = -1e+18 
  CTO = 0.0689 
  TOXPO =  3e-09 
  NOVO = 3.3e+19 
  NOVDO =  1e+20 
  VFBO = -0.0624 
  VFBL = -0.02608 
  VFBLEXP = 0.4021 
  VFBW = -0.003484 
  VFBLW = -5.601e-05 
  VFBBO = -0.1413 
  VFBLBO = 0.002616 
  STVFBO = 3.1e-05 
  STVFBL =  0.165 
  STVFBW =   0.08 
  STVFBLW = -0.0015 
  CICFO = 0.9948 
  CICO =   1.18 
  PSCEL =    0.5 
  PSCELEXP =  0.966 
  PSCEW = 0.02608 
  PSCEBO = 0.9648 
  CFL = 0.6334 
  CFLEXP =  1.388 
  CFW = -0.008173 
  CFBO = 0.00769 
  STCFO =      0 
  CFDO = 0.3187 
  UO = 0.07495 
  BETNL =      0 
  BETNLEXP =      1 
  BETNW =      0 
  BETNBO =  1.311 
  STBETO =   1.05 
  STBETL = -0.015 
  STBETW = 0.00656 
  STBETLW = -0.0005 
  CSO = 0.0001163 
  CSL = 2.623e-05 
  CSLEXP = 0.08822 
  CSW = 0.002384 
  CSLW = 0.003808 
  STCSO =    1.5 
  THECSO =  2.015 
  STTHECSO = 0.03829 
  CSTHRO =  7.226 
  MUEO = 0.7887 
  STMUEO =    1.4 
  THEMUO =  6.393 
  STTHEMUO =    2.5 
  XCORO = 0.007987 
  XCORL = -7.169e-05 
  XCORLEXP =      1 
  XCORW = 0.02777 
  XCORLW = 0.0366 
  STXCORO =  1e-06 
  FETAO = 0.2009 
  RSW1 =  50.31 
  RSW2 =  0.182 
  STRSO = -0.1485 
  RSGO = 0.03429 
  THERSGO = 0.004467 
  THESATO = 0.0001104 
  THESATL = 0.6026 
  THESATLEXP = 0.7241 
  THESATW = 0.04012 
  THESATLW = -0.0007891 
  STTHESATO =  -0.67 
  STTHESATL =  0.005 
  STTHESATW =   0.01 
  STTHESATLW = -0.0006 
  THESATGO =  1.582 
  THESATBO =  3.117 
  AXO =   9.72 
  AXL = 0.001274 
  AXLEXP = 0.0269 
  ALPL1 = 0.0032 
  ALPLEXP =   0.69 
  ALPL2 = 0.0001 
  ALPW =   0.03 
  ALP1L1 =      0 
  ALP1LEXP =    0.5 
  ALP1L2 =      0 
  ALP1W =      0 
  VPO = 0.00055 
  GCOO =     10 
  IGINVLW = 4.95e+07 
  IGOVINVW =  313.6 
  IGOVINVDW =      0 
  IGOVACCW =   1922 
  IGOVACCDW =      0 
  STIGO =  3.304 
  GC2CHO =  0.482 
  GC3CHO = 0.06861 
  GC2OVO =  0.375 
  GC3OVO =  0.063 
  CHIBO =    2.1 
  AGIDLW =  1.079 
  AGIDLDW =  0.001 
  BGIDLO =  13.93 
  BGIDLDO =     14 
  STBGIDLO = 0.001414 
  STBGIDLDO =      0 
  CGIDLO =      0 
  CGIDLDO =      0 
  CGBOVL = 3.5e-16 
  LOVO = 1.75e-10 
  LOVDO =      0 
  CFRO = 2.459e-18 
  CFRDO =      0 
  CFRW = 3.064e-16 
  CFRDW =      0 
  CSDO =      1 
  CSDBPO =      0 
  RTHO = 3.184e+05 
  RTHL =   13.8 
  RTHW =    3.2 
  RTHLW =   11.8 
  STRTHO =      0 
  CTHO = 2.08e-14 
  FNTO =      1 
  NFALW = 4.5e+25 
  NFBLW = 2.828e+08 
  NFCLW = 8.3e-09 
  NFEO =      0 
  NFEBO =      0 
  EFO =    0.9 
  SAREF = 1.8e-06 
  SBREF = 1.8e-06 
  WLOD =      0 
  KUO = 1.714e-09 
  KVSAT =      0 
  TKUO =      0 
  LKUO =      0 
  WKUO =      0 
  PKUO =      0 
  LLODKUO =      0 
  WLODKUO =      0 
  KVTHO = -1.2e-09 
  LKVTHO =      0 
  WKVTHO =      0 
  PKVTHO =      0 
  LLODVTH =      0 
  WLODVTH =      0 
  STETAO =      0 
  LODETAO =      1 
  PSCEDLL =      0 
  PSCEDLW =      0 
  PNCEW =  0.015 
  STCFL =      0 
  CFDLL =      0 
  CFDLW =      0 
  FBET1 =  54.97 
  FBET1W = 0.1039 
  LP1 =  1e-10 
  LP1W = 7.224e-17 
  FBET2 =  6.127 
  LP2 = 1.298e-08 
  BETW1 = 0.01193 
  BETW2 = 0.02188 
  WBET = 1.001e-08 
  GC2OVINVO =   0.74 
  GC3OVINVO =   0.45 
  GC2OVACCO =    0.4 
  GC3OVACCO =      0 
  NIGINVO = 0.05168 
  COVDLO =      0 
  COVDLW =      0 
  DVFBOVO =      0 
 Model file: C:\Users\Xsj\Desktop\test\77K_model\LVT.lib
 Model line:          853
 
Model Name: lvtpfet 

 UTSOI Model(Level76)

 UTSOI VERSION    2.1 

  SWSCALE =      1 
  SWCLIPCHK =      0 
  SWSUBDEP =      1 
  SWIGATE =      1 
  SWGIDL =      1 
  SWSHE =      0 
  SWIGN =      0 
  SWJUNASYM =      0 
  QMC =      1 
  TYPE = -1 
  TR =   -196 
  TMAX =    150 
 Local Model 

  TOXE =  2e-09 
  TSI =  1e-08 
  XGE =      0 
  TBOX =  1e-07 
  NSUB =  3e+18 
  CT =      0 
  TOXP =  2e-09 
  NOV =  1e+20 
  NOVD =  1e+20 
  VFB =      0 
  VFBB =      0 
  STVFB =      0 
  CICF =      1 
  CIC =      1 
  PSCE =      0 
  PSCEB =      1 
  CF =      0 
  CFB =      1 
  STCF =      0 
  CFD =    0.2 
  BETN =   0.05 
  BETNB =      1 
  STBET =    1.5 
  CS =      0 
  STCS =      0 
  THECS =    1.5 
  STTHECS =      0 
  CSTHR =      2 
  MUE =      0 
  STMUE =      0 
  THEMU =    1.5 
  STTHEMU =      0 
  XCOR =      0 
  STXCOR =      0 
  FETA =      1 
  RS =     30 
  STRS =      0 
  RSG =      0 
  THERSG =      2 
  THESAT =      0 
  STTHESAT =   -0.1 
  THESATG =      0 
  THESATB =      0 
  AX =      8 
  ALP =      0 
  ALP1 =      0 
  VP =   0.05 
  GCO =      0 
  IGINV =      0 
  IGOVINV =      0 
  IGOVINVD =      0 
  IGOVACC =      0 
  IGOVACCD =      0 
  STIG =      0 
  GC2CH =  0.375 
  GC3CH =  0.063 
  GC2OV =  0.375 
  GC3OV =  0.063 
  CHIB =    3.1 
  AGIDL =      0 
  AGIDLD =      0 
  BGIDL =     41 
  BGIDLD =     41 
  STBGIDL =      0 
  STBGIDLD =      0 
  CGIDL =      0 
  CGIDLD =      0 
  AREAQ =  1e-12 
  CGBOV =      0 
  COV =      0 
  COVD =      0 
  CFR =      0 
  CFRD =      0 
  CSD = 1.04e-18 
  CSDBP =      0 
  RTH =  1e+04 
  STRTH =      0 
  CTH =  1e-11 
  FNT =      1 
  NFA =  8e+22 
  NFB =  3e+07 
  NFC =      0 
  NFE =      0 
  NFEB =      0 
  EF =      1 
  PSCEDL =      0 
  PNCE =      0 
  CFDL =      0 
  GC2OVINV =  0.375 
  GC3OVINV =  0.063 
  GC2OVACC =  0.375 
  GC3OVACC =  0.063 
  NIGINV =      0 
  COVDL =      0 
  DVFBOV =      0 
 Global Model 

  LVARO =  4e-09 
  LVARL =      0 
  LVARW =      0 
  LAP = 1.391e-09 
  WVARO =  2e-08 
  WVARL =      0 
  WVARW =      0 
  WOT =      0 
  DLQ =  3e-09 
  DWQ = -3e-09 
  TOXEO = 1.095e-09 
  TSIO = 6.9e-09 
  XGEO = 0.2898 
  TBOXO = 2.8e-08 
  NSUBO = 1.774e+18 
  CTO =  1.046 
  TOXPO =  3e-09 
  NOVO =  1e+19 
  NOVDO =  1e+20 
  VFBO = -0.319 
  VFBL = 0.001206 
  VFBLEXP =  1.322 
  VFBW = -0.00986 
  VFBLW = 3.223e-05 
  VFBBO = -0.02468 
  VFBLBO = 0.009052 
  STVFBO = 0.00013 
  STVFBL = -0.011 
  STVFBW = -0.002 
  STVFBLW = 0.0048 
  CICFO =      1 
  CICO = 0.9562 
  PSCEL =   1.89 
  PSCELEXP =  1.552 
  PSCEW = -0.01914 
  PSCEBO =      0 
  CFL = 0.1367 
  CFLEXP =  2.015 
  CFW = 0.0009569 
  CFBO =   12.5 
  STCFO =      0 
  CFDO = 0.6812 
  UO = 0.01998 
  BETNL =      0 
  BETNLEXP =      1 
  BETNW =      0 
  BETNBO =  1.267 
  STBETO =   1.24 
  STBETL = 0.0043 
  STBETW = -0.003 
  STBETLW = -0.0001 
  CSO =  3.566 
  CSL = 0.01172 
  CSLEXP =  2.567 
  CSW = -0.06873 
  CSLW = -0.0001581 
  STCSO =    1.8 
  THECSO =  2.765 
  STTHECSO =      0 
  CSTHRO =   4.33 
  MUEO = 0.01451 
  STMUEO =      1 
  THEMUO = 0.3588 
  STTHEMUO =      0 
  XCORO = 0.02406 
  XCORL = 0.001883 
  XCORLEXP =  0.467 
  XCORW = -0.01295 
  XCORLW = 0.0001631 
  STXCORO =    0.1 
  FETAO = 0.06101 
  RSW1 =  156.5 
  RSW2 = -0.02326 
  STRSO =      0 
  RSGO = 0.0007008 
  THERSGO = 0.001002 
  THESATO = 0.0009958 
  THESATL =  4.115 
  THESATLEXP =  1.222 
  THESATW = -0.007854 
  THESATLW = -0.0001062 
  STTHESATO =   -0.4 
  STTHESATL = -0.009 
  STTHESATW =      0 
  STTHESATLW = -0.0008 
  THESATGO = 0.001012 
  THESATBO = 0.08572 
  AXO =  1.837 
  AXL =  0.142 
  AXLEXP =  0.657 
  ALPL1 = 0.0031 
  ALPLEXP =   0.97 
  ALPL2 = 0.00027 
  ALPW = -0.015 
  ALP1L1 =      0 
  ALP1LEXP =    0.5 
  ALP1L2 =      0 
  ALP1W =      0 
  VPO = 0.0005335 
  GCOO = -2.226 
  IGINVLW = 3.21e+07 
  IGOVINVW = 3.401e+06 
  IGOVINVDW =    107 
  IGOVACCW = 2.051e+04 
  IGOVACCDW =      0 
  STIGO =    1.8 
  GC2CHO = 0.1711 
  GC3CHO = 0.4016 
  GC2OVO =  0.375 
  GC3OVO =  0.063 
  CHIBO =    2.6 
  AGIDLW = 3.333e-05 
  AGIDLDW =  0.001 
  BGIDLO =      7 
  BGIDLDO =     14 
  STBGIDLO = -0.001 
  STBGIDLDO =      0 
  CGIDLO =      0 
  CGIDLDO =      0 
  CGBOVL =  6e-16 
  LOVO =  5e-09 
  LOVDO =      0 
  CFRO = 1.3e-18 
  CFRDO =      0 
  CFRW = 2.265e-16 
  CFRDW =      0 
  CSDO =      1 
  CSDBPO =      0 
  RTHO = 5.594e+05 
  RTHL =   29.7 
  RTHW =    5.8 
  RTHLW =   11.8 
  STRTHO =      0 
  CTHO = 1.18e-14 
  FNTO =      1 
  NFALW =  1e+25 
  NFBLW = 1.71e+09 
  NFCLW = 5.8e-09 
  NFEO =      0 
  NFEBO =      0 
  EFO =      1 
  SAREF = 1.8e-06 
  SBREF = 1.8e-06 
  WLOD =      0 
  KUO =      0 
  KVSAT =      0 
  TKUO =      0 
  LKUO =      0 
  WKUO =      0 
  PKUO =      0 
  LLODKUO =      0 
  WLODKUO =      0 
  KVTHO =      0 
  LKVTHO =      0 
  WKVTHO =      0 
  PKVTHO =      0 
  LLODVTH =      0 
  WLODVTH =      0 
  STETAO =      0 
  LODETAO =      1 
  PSCEDLL =      0 
  PSCEDLW =      0 
  PNCEW =   0.01 
  STCFL = -0.00065 
  CFDLL =      0 
  CFDLW =      0 
  FBET1 =   71.9 
  FBET1W = 0.2032 
  LP1 = 2.953e-10 
  LP1W = 1.018e-19 
  FBET2 =  2.636 
  LP2 = 1.55e-10 
  BETW1 = 0.0001754 
  BETW2 = -0.01553 
  WBET = 0.01135 
  GC2OVINVO =   0.94 
  GC3OVINVO =    1.5 
  GC2OVACCO =   0.35 
  GC3OVACCO =  0.063 
  NIGINVO =   0.15 
  COVDLO =      0 
  COVDLW =      0 
  DVFBOVO =      0 
  **info** (C:\Users\Xsj\Desktop\test\inv_osc.sp:63) DC voltage reset to initial transient source value in source 0:vdda. new dc=0.0000D+00
  
 ******  
 *###############

 ****** element node table
                                                                                      
 0                vbhigh           vblow            vdda             vgndan           
 n1               8:rg             9:rg             20:csdext        21:csdext        
                  20:m1:d          21:m1:d          ic1                               
 n2               10:rg            11:rg            8:csdext         9:csdext         
                  8:m1:d           9:m1:d                                             
 n3               12:rg            13:rg            10:csdext        11:csdext        
                  10:m1:d          11:m1:d                                            
 n4               14:rg            15:rg            12:csdext        13:csdext        
                  12:m1:d          13:m1:d                                            
 n5               16:rg            17:rg            14:csdext        15:csdext        
                  14:m1:d          15:m1:d                                            
 n6               18:rg            19:rg            16:csdext        17:csdext        
                  16:m1:d          17:m1:d                                            
 n7               20:rg            21:rg            18:csdext        19:csdext        
                  18:m1:d          19:m1:d                                            
 vdd              9:csdext         11:csdext        13:csdext        15:csdext        
                  17:csdext        19:csdext        21:csdext        vdda             
                  9:m1:s           11:m1:s          13:m1:s          15:m1:s          
                  17:m1:s          19:m1:s          21:m1:s                           
 vnw              vbhigh           9:m1:b           11:m1:b          13:m1:b          
                  15:m1:b          17:m1:b          19:m1:b          21:m1:b          
 vpw              vblow            8:m1:b           10:m1:b          12:m1:b          
                  14:m1:b          16:m1:b          18:m1:b          20:m1:b          
 vss              8:csdext         10:csdext        12:csdext        14:csdext        
                  16:csdext        18:csdext        20:csdext        vgndan           
                  8:m1:s           10:m1:s          12:m1:s          14:m1:s          
                  16:m1:s          18:m1:s          20:m1:s                           
 x1.xmmn1.gi      8:rg             8:m1:g                                             
 x1.xmmp1.gi      9:rg             9:m1:g                                             
 x2.xmmn1.gi      10:rg            10:m1:g                                            
 x2.xmmp1.gi      11:rg            11:m1:g                                            
 x3.xmmn1.gi      12:rg            12:m1:g                                            
 x3.xmmp1.gi      13:rg            13:m1:g                                            
 x4.xmmn1.gi      14:rg            14:m1:g                                            
 x4.xmmp1.gi      15:rg            15:m1:g                                            
 x5.xmmn1.gi      16:rg            16:m1:g                                            
 x5.xmmp1.gi      17:rg            17:m1:g                                            
 x6.xmmn1.gi      18:rg            18:m1:g                                            
 x6.xmmp1.gi      19:rg            19:m1:g                                            
 x7.xmmn1.gi      20:rg            20:m1:g                                            
 x7.xmmp1.gi      21:rg            21:m1:g                                            
 *****************************************************************
 ******  option summary
 ******
 runlvl  = 3         bypass  = 2.0000    
  Opening plot unit= 79
 file=C:\Users\Xsj\Desktop\test\output.pa0

 ******  
 *###############

 ****** operating point information tnom=  25.000 temp=-196.000 ******
 ****** operating point status is voltage   simulation time is     0.     
     node    =voltage       node    =voltage       node    =voltage

 + 0:n1      = 999.9990m  0:n2      = 706.8680p  0:n3      =  19.6404f
 + 0:n4      =  38.4812a  0:n5      =   1.3813p  0:n6      =  49.7019n
 + 0:n7      =   1.7985m  0:vdd     =   0.       0:vnw     =  -2.0000 
 + 0:vpw     =   2.0000   0:vss     =   0.       8:gi      = 999.9990m
 + 9:gi      = 999.9990m 10:gi      = 706.8680p 11:gi      = 706.8680p
 +12:gi      =  19.6404f 13:gi      =  19.6404f 14:gi      =  38.4812a
 +15:gi      =  38.4812a 16:gi      =   1.3813p 17:gi      =   1.3813p
 +18:gi      =  49.7019n 19:gi      =  49.7019n 20:gi      =   1.7985m
 +21:gi      =   1.7985m

 ******
 *###############

 ****** transient analysis tnom=  25.000 temp=-196.000 ******
 power= -78.3960u  from=   5.0000n     to=  30.0000n

          ***** job concluded
 ******  
 *###############

 ****** job statistics summary tnom=  25.000 temp=-196.000 ******

  ******  PrimeSim HSPICE Threads Information  ******

  Command Line Threads Count :     1
  Available CPU Count        :     8
  Actual Threads Count       :     1


  ******  Circuit Statistics  ******
  # nodes       =      26 # elements   =      46
  # resistors   =      14 # capacitors =      14 # inductors   =       0
  # mutual_inds =       0 # vccs       =       0 # vcvs        =       0
  # cccs        =       0 # ccvs       =       0 # volt_srcs   =       4
  # curr_srcs   =       0 # diodes     =       0 # bjts        =       0
  # jfets       =       0 # mosfets    =      14 # U elements  =       0
  # T elements  =       0 # W elements =       0 # B elements  =       0
  # S elements  =       0 # P elements =       0 # va device   =       0
  # vector_srcs =       0 # N elements =       0


  ******  Runtime Statistics (seconds)  ******

  analysis           time    # points   tot. iter  conv.iter
  op point           0.00           1           5
  transient          6.70       30001       52357       16160 rev=         0
  readin             0.06
  errchk             0.02
  setup              0.00
  output             0.00


           peak memory used         92.16 megabytes
           total cpu time            6.79 seconds
           total elapsed time       15.09 seconds
           job started at     00:16:23 02/09/2024
           job ended   at     00:16:38 02/09/2024
           job total runtime        15.09 seconds


  lic: Release hspice token(s) 
 lic: total license checkout elapse time:        8.30(s)
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

