
*This is the public repo of [grantslab/shinyapps](https://github.com/grantslab/shinyapps) for documentation. The link below referring to database and script files are not accessible from this public repo* 

# Shiny Applications
<!-- TOC depthFrom:2 depthTo:4 withLinks:1 updateOnSave:1 orderedList:0 -->

- [GCI](#gci)
	- [How-To](#how-to)
	- [Releases](#releases)
	- [Development](#development)
- [pancreatic Hi-C GCI](#pancreatic-hi-c-gci)
- [immuneV2G](#immunev2g)
	- [Development](#development)

<!-- /TOC -->

## GCI

GCI application can predict effector target genes with given variant(s) , or predict cis-regulatory elements with a given gene, in various of cell type database generated in SFG. The current [cell type database](https://github.com/grantslab/shinyapps/tree/main/GCI/db/ori_file_path.xlsx) used promoter capture C data (1frag and 4frag) and ATAC-seq data to build cis-regulatory-element-target-gene maps for more than 30 cell types sequenced in SFG. 

### How-To

1. open [GCI app](https://rstudio-connect.chop.edu/connect/#/apps/1890)
  - require CHOP credentials
  - for the first time user, it requires [app admin](mailto:suc1@chop.edu) to assign viewer permission.
  - Note: It will take 1~2 mins to load UI and base database.

2. select cell(s) using `Cells` dropdown menu you want to search.
3. If you want to search a SNP, select "SNP" under `Please select search item`; then open `SNP` dropdown tab to select corresponding parameters
  - select `Single SNP` or `SNiPA output file` for your input format.
    - single SNP search requires LDlink API token. LDlink API token can be obtained by [application](https://ldlink.nci.nih.gov/?tab=apiaccess). Save this token somewhere safe. You will reuse this token for all the future single SNP search on GCI.
    - `SNiPA output file` requires result csv from [SNiPA proxy search](https://snipa.helmholtz-muenchen.de/snipa/?task=proxy_search) where users find proxies for given sentinel SNP(s). It is quite straight forward. You can use "Load example" for familiar yourself with the SNiPA. After getting the result by clicking "Start processing this job", go to "Report" tab and download "results file" to your local computer.
  - If you select `Single SNP` above, 
    - put in sentinel SNP (either rsid or hg19 position in example format).
    - put in API token you obtained from LDlink if you selected `search proxy`
    - select reference population for proxy search
  - If you select `SNiPA output file` above,
    - upload SNiPA output file directly to the GCI app.
  - select rsquare cutoff
  - select proxy search window size
  - select result filters 
    - check `protein coding only` will only report protein-coding genes
    - check `open gene only` will only report genes with open promoter which -1500bp~500bp of TSS overlaps with ATAC-seq peak in corresponding cell types
  - make sure `SNP` is selected and click `search` button
  
4. If you want to search a GENE, select "GENE" under `Please select search item`; then open `GENE` dropdown tab to select
  - type gene name.  
  - select corresponding gene id.
  - select result filter
    - If `OtherEnd must be open` is selected, the reported cis-regulatory elements will be ATAC-seq peak
    - If `OtherEnd must be open` is selected, the reported cis-regulatory elements will be gene connected genomic regions just using promoter capture C data.
  - select output plot type `arc` or `heatmap`
  - make sure `GENE` is selected and click `search` button

### Releases

##### [GCI_v05](https://rstudio-connect.chop.edu/connect/#/apps/1890) updates:

- change proxy search database from myvariant to [LDlink](https://ldlink.nci.nih.gov/?tab=apiaccess). 
  - The usage of LDlink to search requires API token which can be applied [here](https://ldlink.nci.nih.gov/?tab=apiaccess)
  - The myvariant database has been retired and reports error at proxy search
  - All old versions of GCI are retired accordingly.
- add new UI widget allow to upload pr-calculated proxy result csv from [SNiPA](https://snipa.helmholtz-muenchen.de/snipa/?task=proxy_search)
  - proxy file uploading allows to search multiple sentinels at same time.
- switch feather read to vroom read to save database loading time and storage space

##### [GCI_v04](https://rstudio-connect.chop.edu/connect/#/apps/1415) updates:

- add 6 new cell types: Th1_activated, Th2_activated, Th17_activated, Treg_activated, ME_EUR (melanocytes in european), ME_YRI (melanocytes in Nigeria)
- speed up table merging (replace tibble calculation to data.table calculation)
- speed up app loading by seperating database loading from app loading
- speed up database loading (use data.table and feather object). It will be even faster if limited cells are selected. 

Notes: 
1. Th1_activated, Th2_activated, Th17_activated, Treg_activated are using same promoter capture C as Th1, Th2, Th17 and Treg, but ATAC-seq libraries are from activated Th cells.
2. If you search multiple snps or genes among different cell types, it is better to load all the cells at beginning, then never touch the cell specification until you are done with all your searches. Each time you re-select the cells, the database will be reloaded (although it will be much quicker than used-to-be). 

Future update: proxy calculation is still very slow. I will work on speeding up that part in the future updates.

### Development

- The cell database is saved at `/mnt/isilon/sfgi/suc1/analyses/shinyapp_db`
- [current database filepaths](https://github.com/grantslab/shinyapps/tree/main/GCI/db/ori_file_path.xlsx)
- [How to build database](https://github.com/grantslab/shinyapps/tree/main/GCI/db/db_prep.md)


## pancreatic Hi-C GCI

Similar to GCI application, pancreatic Hi-C GCI app can predict effector target genes with given variant(s) , or predict cis-regulatory elements with a given gene, in three pancreatic cell types. Cell type database used Hi-C data (1frag and 4frag) and single cell ATAC-seq data to build cis-regulatory-element-target-gene maps for pancreatic acinar, alpha and beta. The maps were generated used 3 different v2g algorithm:
- binary Hi-C loop call (Fit-HiC-2 and Mustache)
- ABC models
- gene promoter OCR

For more information, refer to recent [preprint](https://www.biorxiv.org/content/biorxiv/early/2021/12/01/2021.11.30.470653.full.pdf).

Current pancreatic Hi-C GCI were hosted at both [CHOP rstudio-connect](https://rstudio-connect.chop.edu/connect/#/apps/2264) and [SFGI shiny webserver](https://sfgishiny.research.chop.edu/V2GPancHic/). Both servers can only be accessed in CHOP network so far (and CHOP rstudio-connect requires additional CHOP credential and user permission from [publisher](mailto:suc1@chop.edu)), but SFGI shiny webserver version will be released to public after paper is published.

## immuneV2G

immuneV2G host a database of pre-calculated V2G mapping which was generated using transcriptomic and epigenomic data (RNA-seq, ATAC-seq, promoter capture-C) from [immune cells](https://github.com/grantslab/shinyapps/tree/main/immunoV2G/db/functional.csv), and variants from GWAS catalog (20200522) for [15 autoimmune diseases](https://github.com/grantslab/shinyapps/tree/main/immunoV2G/db/genetic.csv). It allow users to select autoimmune disease(s) and define r-square, TPM and other filter, and report tables for variant-gene mapping, unique gene list and potential target drugs.

autoimmune diseases:
- multiple sclerosis
- Crohn's disease
- systemic lupus erythematosus
- ulcerative colitis
- rheumatoid arthritis
- ankylosing spondylitis
- inflammatory bowel disease
- type I diabetes mellitus
- Vitiligo
- juvenile idiopathic arthritis
- autoimmune thyroid disease
- psoriatic arthritis
- atopic eczema
- allergy
- psoriasis

cell types:
- GCB from tonsil
- naive B from tonsil
- monocyte
- naive T from tonsil
- TFH from tonsil
- Th1 (rest and activated)
- Th2 (rest and activated)
- Th17 (rest and activated)
- Treg (rest and activated)
- TR1 (rest and activated)

### Development

- v2g database is saved at `/mnt/isilon/sfgi/suc1/analyses/wells/captureC/Promoterome/comparison/immunePanel2/snp/gross_v2g`
- gwas proxy calculation is saved at `/mnt/isilon/sfgi/suc1/gwascatolog/20200522/trait_proxy/`
- [Database generation script](https://github.com/grantslab/shinyapps/tree/main/immunoV2G/db/create_tables.R)

