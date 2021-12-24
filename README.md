# Shared CGED-Q resources
This is a collection of resources to facilitate use of the CGED-Q JSL Public Releases. This will include code snippets for recoding or otherwise handling unusual cases, tables for recoding/categorization/cleanup of variables. 

The materials here are not part of the official data release. Use at your own risk. They may change as we release more data, or as we discover issues. 

The CGED-Q JSL Public Releases are a [Lee-Campbell Group project](https://www.shss.ust.hk/lee-campbell-group). Please visit our website to learn more about our projects and research outputs.

[Introduction to the CGED at the Lee-Campbell Group Website](https://www.shss.ust.hk/lee-campbell-group/projects/china-government-employee-database-qing-cged-q/)

# Sites for CGED-Q JSL Public Release download

[HKUST Dataspace](https://dataspace.ust.hk/dataset.xhtml?persistentId=doi:10.14711/dataset/E9GKRS)

[Harvard Dataverse](https://doi.org/10.7910/DVN/GMQWVZ)

[Renmin University Institute of Qing History](http://39.96.59.69/DownloadFile/DLFile)

# Recode tables

[出身 Chushen recode table - as a Stata .dta file](<CGED-Q JSL Public Release Chushen Recodes.dta>)

Through a merge, this groups the original 出身 chushen into a limited number of categories, with two levels of detail. 

This file was originally created by Bijia Chen, and was added to by Cameron Campbell. 

Example of use, assuming one of the public release files is already loaded in STATA, and the Chushen recodes file is placed in the current working directory:

```
merge m:1 出身一 using "CGED-Q JSL Public Release Chushen Recodes.dta", keep(match master)
```

[籍貫省 Province of origin recode table - as a Stata .dta file](<CGED-Q JSL Public Release Province of Origin 籍貫省 Recodes.dta>)

Through a merge, this helps clean up and make consistent the original 籍貫省 province of origin. This does not handle 江南 since allocating locations there requires inspection of the names of the county of origin 籍貫縣. 

Example of use, assuming one of the public release files is already loaded in STATA, and the Chushen recodes file is placed in the current working directory:

```
merge m:1 籍贯省 using "CGED-Q JSL Public Release Province of Origin 籍貫省 Recodes.dta",keep(match master)
```


