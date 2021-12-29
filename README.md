# Resources for users of the CGED-Q JSL Public Release

This is a collection of resources to facilitate use of the CGED-Q JSL Public Releases. This will include code snippets for recoding or otherwise handling unusual cases, tables for recoding/categorization/cleanup of variables. 

The materials here are not part of the official data release. Use at your own risk. They may change as we release more data, or as we discover issues. 

The CGED-Q JSL Public Releases are a [Lee-Campbell Group project](https://www.shss.ust.hk/lee-campbell-group). Please visit our website to learn more about our projects and research outputs.

[Introduction to the CGED at the Lee-Campbell Group Website](https://www.shss.ust.hk/lee-campbell-group/projects/china-government-employee-database-qing-cged-q/)

# Sites for CGED-Q JSL Public Release download

At these locations you can download the CGED-JSL Public Releases, along with documentation:

[HKUST Dataspace](https://dataspace.ust.hk/dataset.xhtml?persistentId=doi:10.14711/dataset/E9GKRS)

[Harvard Dataverse](https://doi.org/10.7910/DVN/GMQWVZ)

[Renmin University Institute of Qing History](http://39.96.59.69/DownloadFile/DLFile)

# Flags to use when selecting records for analysis 

## Empty posts
When posts were vacant, the Jinshenlu still listed them, but the names of the officeholders were not filled in, or covered over. Normally we want to exclude records of empty posts when analyzing characteristics of officials. We do so by creating a flag variable to identify records of empty posts:

```
generate byte empty_post = 名 == "空白" | 名 == "涂黑" 
```

## Banner versus non-Banner officials 

Because the appointment and promotion of Banner (旗人) officials was very different from that of non-Banner officials (民人), we usually analyze them separately. We create flag variable to identify them based on the contents of four variables that normally are only filled in for Bannermen: 旗分, 身份一, 身份二, 爵位. We also anyone who doesn't have a surname recorded is a Bannermen: 

```
generate byte 旗人 = 旗分 != "" | (姓 == "") | (身份一 != "") | (身份二 != "") | (爵位 != "")
```

## Central versus non-Central government officals

The characteristics of officials who served in the central government (京官) different from those who served in the provinces (外官). We generally distinguish them in analyses.

```
gen byte 京官 =  地区 == "京師" | 地区 == "內務府" | (地区 == "盛京" & (机构一 == "刑部衙門" | 机构一 == "戶部衙門" | 机构一 == "工部衙門" | 机构一 =="禮部衙門"| 机构一 =="兵部衙門"))
```

## Posts that only appear in commercial editions 坊刻本

Certain posts were almost never recorded in official editions 官刻本 that were produced by the government, and only appeared in commercial editions 坊刻本 produced by publishing houses. See the *User Guide* for details. The CGED-Q includes a mixture of commercial and government editions. When constructing time trends in the composition of officials, excluding posts that were only recorded in commercial editions will produce more stable results, with fewer fluctuations from one edition to the next in the numbers of posts, or characteristics of the officials who held them. 

Through a labor-intensive and rather iterative process of looking at the distributionss of posts according to whether they are in commercial or government editions, we have developed code to flag the posts that only only seem to appear in commercial editions. *Use at your own risk. Our approach was ad hoc, and to put it kindly, inductive.*

```

local 干支 "丁卯 丁巳 丁未 丁酉 丁醜 丙午 丙子 丙戌 丙申 丙辰 乙亥 乙卯 乙巳 乙未 乙酉 乙醜 乙未 壬午 壬子 壬寅 壬戌 壬申 壬辰 己亥 己卯 己巳 己未 己酉 己醜 巳卯 巳巳 巳醜 庚午 庚子 庚寅 庚戌 庚申 庚辰 庚寅 庚辰 壬辰 戊午 戊子 戊寅 戊戌 戊申 戊辰 戊戌 甲午 甲子 甲寅 甲戌 甲辰 甲午 甲辰 癸亥 癸卯 癸巳 癸未 癸酉 癸醜 癸未 丙戌 丙子 辛亥 辛卯 辛巳 辛未 辛酉 辛醜"

generate byte 官刻本官职 = 机构一 == "禦前侍衛" | 机构一 == "神機營" | 机构一 == "總管內務府衙門" | 地区 == "內務府" | 地区 == "內務府" | ustrpos(机构一,"旗都統") > 0 | (ustrpos(机构一,"國子監") > 0 & (ustrpos(官职一,"教習") > 0 | ustrpos(官职一,"旗") > 0)) | 	(官职一 == "庫使" & (机构一 == "工部衙門" | 机构一 == "戶部衙門"))
	
replace 官刻本官职 = 官刻本官职 | 机构二 == "藍翎侍衛" | 机构二 == "刑部額外司員" | 机构二 == "工部額外司員" | 机构二 == "花翎侍衛" | 机构二 == "禮部額外司員" | 机构二 == "坐粮廳衙門" | 机构二 == "坐粮㕔衙門" | 机构二 == "從九品吏目" | 	机构二 == "禦前侍衛" | ustrpos(机构二,"額外")

replace 官刻本官职 = 官刻本官职 | 机构三 == "額外中書舍人"

replace 官刻本官职 = 官刻本官职 | 官职一 == "禦前侍衛" | 官职一 == "翰林院五經博士" | 官职一 == "花翎侍衛" | 官职一 == "藍翎侍衛" | usubstr(官职一,-2,.) == "行走" | 官职一 == "額外序班" | 官职一 == "司樂" | 官职一 == "額外中書舍人" 

replace 官刻本官职 = 官刻本官职 | 官职一 == "武英殿校録" | 官职一 == "額外經厯" | 官职一 == "額外待詔" | 官职一 == "額外典簿" | 官职一 == "額外主簿" | 官职一 == "揀發副指揮" | 官职一 == "額外主簿" | 官职一 == "揀發吏目" | 官职一 == "特授正指揮" | 官职一 == "揀發正指揮" | 官职一 == "特授副指揮" | 官职一 == "額外主事" | 官职一 == "特授吏目" | 官职一 == "候補主事" | 官职一 == "額外中書"

foreach x of local 官刻本官职 {
	replace 官刻本官职 = 官刻本官职 | 官职一 == "`x'" | 官职一 == "`x'科" | ///
		官职一 == "`x'" | 官职一 == "`x'科" | 机构二 == "`x'" | 机构二 == "`x'科" | 机构二 == "`x'" | 机构二 == "`x'科"
}

```

## Duplicated records

In the editions that we transcribed from, some pages were repeated, leaving to duplicate records. These should normally be excluded from analysis. We included them in the public release because of our commitment to releasing the data exactly as transcribed. The following commands will flag the duplicate records, allowing for their exclusion or deletion.

```
generate duplicate = 阳历年份 == 1834 & 季节号 == 3 & ((序号 >= 428 & 序号 <= 484) | (序号 >= 541 & 序号 <= 596) | (序号 >= 968 & 序号 <= 1010) | (序号 >= 3190 & 序号 <= 3238) | (序号 >= 3529 & 序号 <= 3573) | (序号 >= 3860 & 序号 <= 3902) | (序号 >= 5058 & 序号 <= 5091) | (序号 >= 5300 & 序号 <= 5323) | (序号 >= 5687 & 序号 <= 5723) | (序号 >= 5969 & 序号 <= 6018) | (序号 >= 6466 & 序号 <= 6501) | (序号 >= 8318 & 序号 <= 8353) | (序号 >= 9179 & 序号 <= 9209) | (序号 >= 9575 & 序号 <= 9607) | (序号 >= 10359 & 序号 <= 10428) | (序号 >= 10904 & 序号 <= 10934) | (序号 >= 12290 & 序号 <= 12324) | (序号 >=13078 & 序号 <= 13114) | (序号 >= 14266 & 序号 <= 14294) | (序号 >= 14547 & 序号 <= 14578) | (序号 >= 14642 & 序号 <= 14674))

replace duplicate = duplicate | (阳历年份 == 1857 & 季节号 == 1 & (序号 >= 11366 & 序号 <= 11376))

replace duplicate = duplicate | (阳历年份 == 1898 & 季节号 == 2 & ((序号 >= 428 & 序号 <= 461) | (序号 >= 604 & 序号 <= 651) | (序号 >= 751 & 序号 <= 802) | (序号 >= 1272 & 序号 <= 1453) | (序号 >= 2133 & 序号 <= 2170) | (序号 >= 2461 & 序号 <= 2487) | (序号 >= 2824 & 序号 <= 2858) | (序号 >= 2976 & 序号 <= 3015) | (序号 >= 3052 & 序号 <= 3128) | (序号 >= 3737 & 序号 <= 3786) | (序号 >= 4078 & 序号 <= 4132) | (序号 >= 4360 & 序号 <= 4427) | (序号 >= 4590 & 序号 <= 4632) | (序号 >= 5169 & 序号 <= 5214) | (序号 >= 5403 & 序号 <= 5453) | (序号 >= 5917 & 序号 <= 5972) | (序号 >= 6317 & 序号 <= 6371) | (序号 >= 6503 & 序号 <= 6521) | (序号 >= 6880 & 序号 <= 6917) | (序号 >= 6946 & 序号 <= 6973) | (序号 >= 7043 & 序号 <= 7081) | (序号 >= 7189 & 序号 <= 7228)))

replace duplicate = duplicate | (阳历年份 == 1898 & 季节号 == 2 & ((序号 >= 7436 & 序号 <= 7469) | (序号 >= 7856 & 序号 <= 7891) | (序号 >= 8374 & 序号 <= 8406) | (序号 >= 8479 & 序号 <= 8515) | (序号 >= 8586 & 序号 <= 8621) | (序号 >= 9515 & 序号 <= 9549) | (序号 >= 9957 & 序号 <= 9989) | (序号 >= 10051 & 序号 <= 10080) | (序号 >= 10733 & 序号 <= 10738) | (序号 >= 10798 & 序号 <= 10832) | (序号 >= 11219 & 序号 <= 11249) | (序号 >= 11306 & 序号 <= 11332) | (序号 >= 11389 & 序号 <= 11415) | (序号 >= 11639 & 序号 <= 11655) | (序号 >= 11671 & 序号 <= 11704) | (序号 >= 11880 & 序号 <= 11918) | (序号 >= 11989 & 序号 <= 12027) | (序号 >= 12061 & 序号 <= 12130) | (序号 >= 12266 & 序号 <= 12267) | (序号 >= 12458 & 序号 <= 12495) | (序号 >= 12641 & 序号 <= 12676) | (序号 >= 13011 & 序号 <= 13045)))

replace duplicate = duplicate | (阳历年份 == 1898 & 季节号 == 2 & ((序号 >= 13403 & 序号 <= 13438) | (序号 >= 14147 & 序号 <= 14220) | (序号 >= 14283 & 序号 <= 14356) | (序号 >= 14384 & 序号 <= 14418) | (序号 >= 14765 & 序号 <= 14795) | (序号 >= 14891 & 序号 <= 14923) | (序号 >= 15374 & 序号 <= 15407) | (序号 >= 16000 & 序号 <= 16034) | (序号 >= 16295 & 序号 <= 16325) | (序号 >= 16731 & 序号 <= 16760)))

replace duplicate = duplicate | (阳历年份 == 1901 & 季节号 == 2 & ((序号 >= 5208 & 序号 <= 5242) | (序号 >= 6939 & 序号 <= 6977) | (序号 >= 10338 & 序号 <= 10373) | (序号 >= 10531 & 序号 <= 10561) | (序号 >= 11108 & 序号 <= 11123) | (序号 >= 11299 & 序号 <= 11337) | (序号 >= 12625 & 序号 <= 12665) | (序号 >= 12799 & 序号 <= 12836) | (序号 >= 13653 & 序号 <= 13657) | (序号 >= 13805 & 序号 <= 13830) | (序号 >= 14513 & 序号 <= 14545)))

replace duplicate = duplicate | (阳历年份 == 1901 & 季节号 == 3 & ((序号 >= 557 & 序号 <= 591) | (序号 >= 726 & 序号 <= 764) | (序号 >= 1112 & 序号 <= 1145) | (序号 >= 2456 & 序号 <= 2497) | (序号 >= 2696 & 序号 <= 2730) | (序号 >= 3365 & 序号 <= 3430) | (序号 >= 3890 & 序号 <= 3941) | (序号 >= 6359 & 序号 <= 6379) | (序号 >= 6482 & 序号 <= 6524) | (序号 >= 6819 & 序号 <= 6845) | (序号 >= 7177 & 序号 <= 7214) | (序号 >= 7792 & 序号 <= 7824) | (序号 >= 7891 & 序号 <= 7923) | (序号 >= 8144 & 序号 <= 8177)  | (序号 >= 8596 & 序号 <= 8633) | (序号 >= 9630 & 序号 <= 9663) | (序号 >= 9838 & 序号 <= 9872) | (序号 >= 9962 & 序号 <= 9992) | (序号 >= 10087 & 序号 <= 10117)))

replace duplicate = duplicate | (阳历年份 == 1901 & 季节号 == 3 & ((序号 >= 11049 & 序号 <= 11063) | (序号 >= 11322 & 序号 <= 11352) | (序号 >= 11716 & 序号 <= 11742) | (序号 >= 11933 & 序号 <= 11953) | (序号 >= 12213 & 序号 <= 12245) | (序号 >= 14053 & 序号 <= 14084) | (序号 >= 14858 & 序号 <= 14890) | (序号 >= 14921 & 序号 <= 14950) | (序号 >= 15373 & 序号 <= 15405) | (序号 >= 15993 & 序号 <= 16025) | (序号 >= 16127 & 序号 <= 16161) | (序号 >= 16654 & 序号 <= 16687) | (序号 >= 16718 & 序号 <= 16747) | (序号 >= 16929 & 序号 <= 16966)))
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




