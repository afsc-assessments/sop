# Data {#data}

## Getting access


*This section was pretty much put together by Jordan Watson, thanks Jordan!!*

There are two databases in particular that you’ll want access to. “AFSC” and “AKFIN”. AKFIN is the Alaska Fisheries Information Network (our version of ACCSP). 
These are both Oracle databases but require different logins and different network connections. 
To get an AFSC account, your supervisor will need to submit a helpdesk request. 
To get an AKFIN account, you will need to contact Bob Ryznar at Pacific States Marine Fisheries Commission (RRyznar@psmfc.org). 
With Bob, specify that you’d like access via both Answers (online GUI) and SQL (it’s two different accounts).

Before getting your accounts, you will need to get on the “signer’s list” which gives you access to confidential data. 
Sign both the federal and state ones which will give you full access to most things in AKFIN (including fish tickets). 
Contact Obren Davis at the AKRO to get the paperwork. 
This will not give you access to VMS data. That’s a separate process. 


**CRITICAL:** If you mess up your password in the AFSC database 3 times, your account is locked and you need the admin to reset you. Seriously. 
It’s a pain. 
You have to change your password every three months. Easy to change in `sql developer`.

**Recommended:** have IT load `SQLDeveloper` on computer.

todo: add examples of changing in sql developer


You will need to have IT install the Oracle odbc driver. 
Take the code chunks below (AFSC and AKFIN) and copy and paste them into a blank text document and save that document as `tnsnames.ora`. 
Give this `.ora` file to the IT person when they are setting up your account. 

```
afsc =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = raja.afsc.noaa.gov)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = afscp1)
    )
  )

akfin =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = akfindb.psmfc.org)(PORT = 2045))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = akfin.psmfc.org)
    )
  )
```
Some simple terminology for when you are communicating with the AKFIN folks. “AKFIN” is the database. The schema is essentially like a folder and inside of it are all the tables.

## Passwords

Your AFSC database password expires every 90 days. 
Fortunately it is easy to update using Oracle SQL Developer. 

Step 1. Open Oracle SQL Developer  
Step 2. Right click the AFSC connection, then click on "Reset Password".

<img src="figs/pwd1.png" title="Screenshot of using SQL Developer to reset a password for the AFSC server." alt="Screenshot of using SQL Developer to reset a password for the AFSC server." width="513" />



## Confidentiality

Some data we just aren't permitted to share...

### Handling confidential data on GitHub

#### Easy method 
Use `.gitignore` and keep any confidential files from being pushed to github.



#### Local commit but not yet pushed to GitHUb

In this scenario you have accidentally **committed the wrong files** to Git, **but haven't pushed the commit to GitHub**. 
There are several ways to "undo" a series of commits. 
The method you choose depends upon the desired outcome: 

*  Undo commit and keep all files staged: `git reset --soft HEAD^`
*  Undo commit and unstage all files: `git reset HEAD^`
    + Undo last 3 commits and unstage all files: `git reset HEAD~3`
*  Undo the commit and completely remove all changes: `git reset --hard HEAD^`

Generally speaking you will want to use `git reset HEAD^`.

<img src="https://www.cloudsavvyit.com/p/uploads/2021/07/f5026f58.png?trim=1,1&bg-color=000&pad=1,1" title="git commands for undoing commits." alt="git commands for undoing commits."  />


#### Accidentally pushed to GitHUb


## Crab data 

## Groundfish data 

### Bering Sea

Stratum numbers in the AKFIN tables designate different levels of data grouping. 
These designations are different for the different surveys (EBS slope vs EBS shelf). 
Given that these groupings are made at different levels, care should be taken not to sum values over all strata because this can duplicate data.

#### EBS SLOPE STRATUM DESIGNATIONS: 

Strata include (`1:6, 11:15, 21:25, 31:35, 41:45, 5:55, 61:65, 999999`).

For 1-digit “strata”, the digit designates the Canyons ordered 1 - 6 , south to north, as follows:

 - `1 - Bering Canyon`
 - `2 - Pribilof Canyon`
 - `3 - Inter Pribilof-Zhemchug`
 - `4 - Zhemchug Canyon`
 - `5 - Inter Zhemchug-Pervenets`
 - `6 - Navarin/Pervenets Canyons`

For 2-digit “strata”, the 1st digit designates the slope Canyon as above, and the 2nd digit designates the depth zone grouped into 200 m ranges as follows:

2nd digit:

 - `200-400 m` depth zone
 - `400-600 m` depth zone
 - `600-800 m` depth zone
 - `800-1000 m `depth zone
 - `1000-1200 m` depth zone

Thus, for example:

Stratum   `1` = Bering Canyon, all depths within that Canyon  
Stratum   `2` = Pribilof Canyon, all depths within that Canyon  
Stratum  `11` = Bering Canyon at depths of 200-400 m  
Stratum  `65` = Navarin/Pervenets Canyons at depths of 1000-1200 m

Finally, there is a combined overall Stratum designation:
`999999` = all Canyons over all depth zones

Thus, biomass for `stratum=999999` is the same as the sum of biomass for strata `1+2+3+4+5+6`, or the sum of biomass for strata (`11+12+13+14+15+21+22+23+24+25+31+32+33+34+35+41+42+43+44+45+51+52+53+54+55+61+62+63+64+65`).


<img src="figs/EBS_slope_strata.png" title="Map of the Bering Sea showing strata, the digit designates the canyons ordered 1-6, south to north" alt="Map of the Bering Sea showing strata, the digit designates the canyons ordered 1-6, south to north" width="800px" height="460px" />

#### EBS SHELF STRATUM DESIGNATIONS:

Strata include (`1:10, 20, 31, 32, 41:43, 50, 61, 62, 82, 90, 100, 200, 300, 999`). 

Please refer to the stratum map for location of strata. 
Strata `10, 20, 31, 32, 41, 42, 43, 50, 61, 62, 82, 90` are the core strata within which biomass and population are computed. 
The other strata (`1:9, 100, 200, 300, 999`) are various summations of the core strata, as follows:

 - `10`
 - `20`
 - `31+32`
 - `41+42+43`
 - `50`
 - `61+62`
 - `82`
 - `90`

`100` = depth zone <50 m (=strata 1+2; or 10+20)  
`200` = depth zone 50-100 m (=strata 3+4+8; or 31+32+41+42+43+82)  
`300` = depth zone 100-200 m (=strata 5+6+9; or 50+61+90)

`999` = overall combined (all strata = `1+2+3+4+5+6+8+9`; or `10+20+31+32+41+42+43+50+61+62+82+90`)

<img src="figs/EBS_shelf_strata.png" title="Easter Bering Sea stratum map showing strata 10, 20, 31-32, 41-43, 50, 61-62m 82, and 90." alt="Easter Bering Sea stratum map showing strata 10, 20, 31-32, 41-43, 50, 61-62m 82, and 90." width="800px" height="460px" />

### Aleutian Islands


### Gulf of Alaska


## AFSC Longline Survey (LLS)

Primary data contacts:
(1) Survey methods and operations: kevin.siwicke@noaa.gov, pat.malecha@noaa.gov
(2) Longline database and raw survey data: cara.rodgveller@noaa.gov, jane.sullivan@noaa.gov
(3) Assessment data products: jane.sullivan@noaa.gov, cara.rodgveller@noaa.gov

Comprehensive documentation for the longline survey can be found on AKFIN Answers and on the AKFIN Stock Assessment Portal: 
https://akfinbi.psmfc.org/analyticsRes/Documentation/Database_Background_Instructions_AKFIN_20210915.pdf

The AFSC also publishes an [annual survey report](https://repository.library.noaa.gov/view/noaa/37523), which includes several useful references on survey history and methods. Maps and detailed descriptions of geographic areas and depth strata can be found in [Echave et al. 2013](https://repository.library.noaa.gov/view/noaa/11869). 

<img src="figs/lls_stations_echave.PNG" width="800px" height="460px" />
Figure reproduced from [Echave 2017](https://media.fisheries.noaa.gov/dam-migration/first-results-of-the-tagging-of-shortspine-thornyhead-508.pdf)

LLS data are primarily accessed from the AKFIN Answers, or directly queried from the akfin or longline databases. 

Available time series:   
Japanese-led survey: 1964-1981
Cooperative Japan/U.S. survey: 1979-1994
Domestic (U.S.) survey: 1988-present. Most stock assessments start the Domestic survey time series in 1990 (e.g. sablefish) or 1992 (e.g. rougheye/blackspotted rockfish) due to inconsistencies in the early data. The Gulf of Alaska is surveyed annually, and since 1996 the Bering Sea and Aleutian Islands have been sampled in odd and even years, respectively. 

Species of interest: Sablefish, shortspine thornyhead, Greenland turbot, Pacific cod, rougheye/blackspotted rockfish, and shortraker rockfish, arrowtooth flounder, Kamchatka flounder, grenadiers, skates, and spiny dogfish. Catch and effort data are recorded for all catch. Length composition data are collected for all aforementioned species. Sex, age, and maturity data are collected for sablefish. Sex data are also collected for grenadiers.

The indices of abundance available for the LLS include the following: CPUE = numbers per skate (1 skate = 45 hooks); relative population numbers (RPNs) = area-weighted CPUE (area estimates are defined by depth strata and geographic area), relative population weight (RPW) = RPN multiplied by mean fish weight, which is calculated using an allometric relationship and the mean length of fish collected in a given strata and geographic area. The methods for variance estimation of these indices are documented on [p 26 the 2016 sablefish SAFE pdf](https://apps-afsc.fisheries.noaa.gov/REFM/Docs/2016/GOAsablefish.pdf) (p 350 of the GOA SAFE). 

The time series starts for the domestic (i.e. operated by the AFSC) LLS in the BS and AI starts in 1996, and there are historical data available in the BS/AI from the cooperative Japanese/U.S. survey. In the modern/domestic survey, the eastern AI are surveyed in even years, and the BS is surveyed in odd years. The LLS does not sample the western AI. Estimates in NW and SW AI are based on fixed ratios in the NE and SE AI, respectively, from historical cooperative Japanese/U.S. surveys in 1979-1994. Note that definitions of eastern and western AI in the LLS are not equivalent to the AI bottom trawl survey definitions. *It is a work in progress to reconcile these survey areas for purposes of comparison.* Survey depths range from ~100 to 1,000 m; depth strata are defined using 100 m increments at depths < 400 m and 200 m increments at depths >= 400 m. These depth strata differ from the AFSC bottom trawl survey definitions, making one-on-one comparisons challenging at depths > 300 m.

<img src="figs/depth_strata.PNG" width="800px" height="460px" />




## IPHC Fisheries Independent Setline Survey (FISS)

