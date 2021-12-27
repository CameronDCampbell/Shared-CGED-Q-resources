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

# Flag for selecting records for analysis 

## Empty posts
When posts were vacant, the Jinshenlu still listed them, but the names of the officeholders were not filled in, or covered over. Normally we want to exclude records of empty posts when analyzing characteristics of officials. We do so by creating a flag variable to identify records of empty posts:

```
generate bye empty_post = 名 == "空白" | 名 == "涂黑" 
```

## Banner versus non-Banner officials 

Because the appointment and promotion of Banner (旗人) officials was very different from that of non-Banner officials (民人), we usually analyze them separately. We create flag variable to identify them based on the contents of four variables that normally are only filled in for Bannermen: 旗分, 身份一, 身份二, 爵位. We also anyone who doesn't have a surname recorded is a Bannermen: 

```
generate byte 旗人 = 旗分 != "" | (姓 == "") | (身份一 != "") | (tab 身份二 != "") | (爵位 != "")
```

## Central government officals versus non-Central government officials

The characteristics of officials who served in the central government (京官) different from those who served in the provinces (外官). We generally distinguish them in analyses.

```
gen byte 京官 =  地区 == "京師" | 地区 == "內務府" | 地区 == "盛京" & (机构一 == "刑部衙門" | 机构一 == "戶部衙門" | 机构一 == "工部衙門" | 机构一 =="禮部衙門"| 机构一 =="兵部衙門")
```

# Recoding and recategorizing variables

## 出身 Chushen

Download [出身 Chushen recode table - as a Stata .dta file](<CGED-Q JSL Public Release Chushen Recodes.dta>)

Through a merge, this groups the original 出身 chushen into a limited number of categories, with two levels of detail. 

This file was originally created by Bijia Chen, and was added to by Cameron Campbell. 

Below is an example  of use. The code assumes of the public release files is already loaded in STATA, and the Chushen recodes file is placed in the current working directory. chushen_category is the detailed chushen category as a string, and chushen_order is a numeric version. chushen_order_2 has broader categories, and chushen_order_2_eng is English.

Remember that in some editions, 进士 and 举人 are not recorded in 出身, which may be blank. In those editions, holders of 进士 or 举人 may be identified by the presence in 科年一 of a 干支 that matches an exam year. Please see the *User Guide* discussion of 出身. In our own work we match 科年一 against exam years to ascertain exam degrees, but this is rather involved, and we are not ready to release the relevant code. 

```
merge m:1 出身一 using "CGED-Q JSL Public Release Chushen Recodes.dta", keep(match master)
replace chushen_category = "其他" if _merge == 1
replace chushen_order_2_eng = "Other" if _merge == 1
replace chushen_order = 12 if _merge == 1
replace chushen_order_2 = 12 if _merge == 1
drop _merge 
```

## 地区 Region of post

The following will help make 地区 more consistent across editions. It does not 'correct' 地区 since it can change from edition to edition based on changes in the definition of administrative units. Rather, it reassigns some regions to group with their most commonly observed administrative unit. For example, some newly formed administrative units are reassigned to their original units, for the purpose of making definitions consistent across editions.
```
generate 地区_original = 地区
replace 地区 = "不顯" if 地区 == "" | 地区 == "503"
replace 地区 = "甘肅省" if 地区 == "甘肅新疆" | 地区 == "甘肅新疆省"
replace 地区 = "盛京" if 地区 == "吉林甯古塔城" | 地区 == "吉林省" | 地区 == "黑龍江城" | 地区 == "黑龍江城" | 地区 == "黑龍江省" | 地区 == "奉天省"
replace 地区 = "江蘇省" if 地区 == "江南省" & (机构一 == "太湖廰" | 机构一 == "常州府" |  机构一 == "徐州府" | 机构一 == "揚州府" | 机构一 == "松江府" | 机构一 == "江寕府" | 机构一 == "江甯府" | 机构一 == "淮安府" | 机构一 == "蘇州府" |  机构一 == "鎭江府" | 机构一 == "鎮江府")
replace 地区 = "江蘇省" if 地区 == "江南省" & (ustrpos(机构一,"海門") | ustrpos(机构一,"太倉") | ustrpos(机构一,"海門") | ustrpos(机构一,"淮安") | ustrpos(机构一,"通州") | ustrpos(机构一,"海州") | ustrpos(机构一,"兩淮") | ustrpos(机构一,"海州") | ustrpos(机构一,"江寕") | ustrpos(机构一,"江甯") | ustrpos(机构一,"江蘇") | ustrpos(机构一,"鎮江")) 
replace 地区 = "陝西省" if 地区 == "陜西省"
replace 地区 = "直隸省" if 地区 == "直隷省"
```

## 籍貫省 Province of origin

Download [籍貫省 Province of origin recode table - as a Stata .dta file](<CGED-Q JSL Public Release Province of Origin 籍貫省 Recodes.dta>)

Through a merge, this file helps clean up and make consistent the original 籍貫省 province of origin. We have included some additional code to handle 江南 Jiangnan when the 籍贯县 county of origin is also available. 
After the merge, the new variable 籍貫省_clean will contain the 'cleaned' province names, and should be suitable for tabulations.

Example of use, assuming one of the public release files is already loaded in STATA, and the Chushen recodes file is placed in the current working directory:

```
merge m:1 籍贯省 using "CGED-Q JSL Public Release Province of Origin 籍貫省 Recodes.dta",keep(match master) nogenerate
replace 籍貫省_clean = "安徽" if (籍贯县 == "亳州" | 籍贯县 =="休寧" | 籍贯县 =="婺源" | 籍贯县 == "宣城" | 籍贯县 == "巢縣" | 籍贯县 =="旌德" | 籍贯县 =="桐城" | 籍贯县 =="歙縣" | 籍贯县 =="涇縣" | 籍贯县 =="滁州" | 籍贯县 =="當塗" | 籍贯县 =="蕪湖" | 籍贯县 == "遂寧" | 籍贯县 == "銅山" | 籍贯县 == "霍邱" | 籍贯县 =="青陽" | 籍贯县 =="靑陽" | 籍贯县 =="太平" | 籍贯县 == "上元") & 籍贯省 == "江南"
replace 籍貫省_clean = "江西" if (籍贯县 == "南豊" | 籍贯县 =="撫州" | 籍贯县 =="鄱陽" | 籍贯县 == "豐城" | 籍贯县 == "萍鄉") & 籍贯省 == "江南"
replace 籍貫省_clean = "浙江" if 籍贯县 == "桐鄉" & 籍贯省 == "江南"

```

After this cleanup, you may notice many provinces of origin are still blank. In the original source, if an official was serving in their province of origin, the province of origin was left blank, and we can assume that their province of origin is where they are currently serving (地区). The following command transfers the contents of 地区 to 籍贯省. It isn't perfect, but it is enough for a first pass.

```
replace 籍貫省_clean = usubinstr(地区,"省","",.) if 籍貫省_clean == "空白"
```

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




