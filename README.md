# Shared CGED-Q JSL Public Release resources
This is a collection of resources to facilitate use of the CGED-Q JSL Public Releases. This will include code snippets for recoding or otherwise handling unusual cases, tables for recoding/categorization/cleanup of variables. 

The materials here are not part of the official data release. Use at your own risk. They may change as we release more data, or as we discover issues. 

The CGED-Q JSL Public Releases are a [Lee-Campbell Group project](https://www.shss.ust.hk/lee-campbell-group). Please visit our website to learn more about our projects and research outputs.

[Introduction to the CGED at the Lee-Campbell Group Website](https://www.shss.ust.hk/lee-campbell-group/projects/china-government-employee-database-qing-cged-q/)

# Sites for CGED-Q JSL Public Release download

At these locations you can download the CGED-JSL Public Releases, along with documentation:

[HKUST Dataspace](https://dataspace.ust.hk/dataset.xhtml?persistentId=doi:10.14711/dataset/E9GKRS)

[Harvard Dataverse](https://doi.org/10.7910/DVN/GMQWVZ)

[Renmin University Institute of Qing History](http://39.96.59.69/DownloadFile/DLFile)

# Recoding and recategorizing

## 出身 Chushen

[出身 Chushen recode table - as a Stata .dta file](<CGED-Q JSL Public Release Chushen Recodes.dta>)

Through a merge, this groups the original 出身 chushen into a limited number of categories, with two levels of detail. 

This file was originally created by Bijia Chen, and was added to by Cameron Campbell. 

Example of use, assuming one of the public release files is already loaded in STATA, and the Chushen recodes file is placed in the current working directory.

chushen_category is the detailed chushen category as a string, and chushen_order is a numeric version. chushen_order_2 has broader categories, and chushen_order_2_eng is English.

```
merge m:1 出身一 using "CGED-Q JSL Public Release Chushen Recodes.dta", keep(match master)
replace chushen_category = "其他" if _merge == 1
replace chushen_order_2_eng = "Other" if _merge == 1
replace chushen_order = 12 if _merge == 1
replace chushen_order_2 = 12 if _merge == 1
drop _merge 
```

## 籍貫省 Province of origin

[籍貫省 Province of origin recode table - as a Stata .dta file](<CGED-Q JSL Public Release Province of Origin 籍貫省 Recodes.dta>)

Through a merge, this helps clean up and make consistent the original 籍貫省 province of origin. I have included some additional code to handle 



Example of use, assuming one of the public release files is already loaded in STATA, and the Chushen recodes file is placed in the current working directory:

```
generate 籍贯省_original = 籍贯省
merge m:1 籍贯省 using "CGED-Q JSL Public Release Province of Origin 籍貫省 Recodes.dta",keep(match master) nogenerate
replace 籍贯省 = "安徽" if (ren_xian == "亳州" |ren_xian =="休寧" | ren_xian=="婺源" | ren_xian == "宣城" | ren_xian=="巢縣" | ren_xian=="旌德" | ren_xian =="桐城" | ren_xian =="歙縣" | ren_xian =="涇縣" | ren_xian =="滁州" | ren_xian =="當塗" | ren_xian=="蕪湖" | ren_xian=="遂寧" | ren_xian=="銅山" | ren_xian=="霍邱" | ren_xian=="青陽" | ren_xian=="靑陽" | ren_xian=="太平") & 籍贯省 == "江南"
replace 籍贯省 = "江西" if (ren_xian == "南豊" | ren_xian =="撫州" | ren_xian =="鄱陽"） & 籍贯省 == "江南"
replace 籍贯省 = "浙江" if ren_xian == "桐鄉" & 籍贯省 == "江南"
replace 籍贯省 = "江蘇" if 籍贯省 == "江南"


```

After the merge, the new variable 籍貫省_clean will contain the 'cleaned' province names, and should be suitable for tabulations.

## 旗分 Banner

The following code will clean up the contents of the 旗分 Banner variable:

```
generate 旗分_original = 旗分 
replace 旗分 = usubinstr(旗分,"旗","",.) if strpos(旗分,"旗人") == 0
replace 旗分 = usubinstr(旗分,"廂","鑲",.) 
replace 旗分 = usubinstr(旗分,"镶","鑲",.)
replace 旗分 = usubinstr(旗分,"红","紅",.)
replace 旗分 = usubinstr(旗分,"蓝","藍",.)
replace 旗分 = usubinstr(旗分,"黃","黄",.)
replace 旗分 = "正藍" if 旗分 == "正藍祺"
replace 旗分 = "鑲白" if 旗分 == "鑲白祺"
replace 旗分 = "不顯" if ustrpos(旗分,"？")
replace 旗分 = "其他" if 旗分 != "" & ustrpos("|不顯|鑲黃|鑲黄|正白|正黃|正黄|正藍|鑲藍|鑲白|鑲紅|正紅|塗黑|","|"+旗分+"|") == 0
```

旗分_original will contain the original contents of 旗分

