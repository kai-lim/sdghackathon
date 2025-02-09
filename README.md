# SDG Hackathon - Visualizing Swiss Sustainable Development Goals

# My submission


<img align="right" src="SDG_2.png">




## Overview

<img align="right" src="text2sdg_logo.png" width="200" height="200">

To goal of the hackathon is to achieve a better understanding of how the Swiss research landscape addresses the UN Sustainable Development Goals (SDGs), as well as of the methods used to map research to SDGs.

We provide a preanalyzed data set of research projects based on the P3 (Projects-People-Publications) of the Swiss National Science Foundation (SNSF). The P3 database contains information on various types of research projects funded by the SNSF. We have used the [text2sdg](https://github.com/dwulff/text2sdg) R package to map project descriptions to the 17 SDGs using five different SDG-labeling systems. 

The following sections provide more information on the data set and the SDG-labeling systems. 


### Data

The source data was downloaded from the [P3 open data page](https://p3.snf.ch/Pages/DataAndDocumentation.aspx) and processed in the following way. First, we eliminated all projects without an abstract, which led to the exclusion of 44,604 projects (58%) including all projects funded prior to 2006. Second, all non-English abstracts were automatically translated to English using the [EasyNMT](https://pypi.org/project/EasyNMT/) Python library. Third, we matched all abstracts to the 17 SDGs using the five labeling systems implemented in the [text2sdg](https://github.com/dwulff/text2sdg) R package. A list of changes to the P3 database can be found in the Appendix below.
   
We provide a main data set called **sdg_hackathon_data.zip** and a supplementary data set called **supplementary_data.zip**. The main data set provides information on key variables of interest, such as the discipline, the funding instrument, the hosting University, and the SDG labels. The supplementary data set includes additional information on the projects that was available in the P3 but are mostly not relevant for the goals of this hackathon. The one exception might be the abstracts of the projects, which were included in the supplementary data to avoid exceeding Githubs size limitations.

### SDG labeling

The projects were labeled using five Lucene-style query systems implemented in the [text2sdg](https://github.com/dwulff/text2sdg) R package. The systems have been developed by different for-profit and non-profit organizations. So far, both independent and comparative evaluations of the quality of these systems are largely missing. However, it is clear that the systems differ in how liberal they assign the 17 SDGs to texts. The Elsevier, SIRIS, and, especially, the Aurora system recruit more complicated queries than SDSN and Ontology and are therefore more conservative (assign fewer SDGs) than the latter two.

The labeling systems also differ in the number of SDGs that they assign. Elsevier and SIRIS only cover the first 16 SDGs excluding SDG 17 Global Partnership, whereas Aurora, SDSN, and Ontology cover all 17 SDGs. Additional detail on each query system is provided in the Appendix below.

<img align="center" src="SDG_icons.png">


## Files
This repository contains three files, listed in the table below. A detailed description of the two data sets can be found in the next section.

|Name|Description|
| ------ | ------ |
| sdg_hackathon_data.zip | Main data set with key variables including the SDG labels |
| supplementary_data.zip | Supplementary data set with addtional variables included in P3 |
| SDG_icons.png | Overview of the 17 SDGs |
| SDG Icons 2019.zip | A folder with the SDG icons that you can use for visualizations |
| text2sdg_logo.png | Logo of the text2sdg package |


## Variables

### Main dataset (`sdg_hackathon_data.zip`)

|Column #|Name|Type|Description|
| ------ | ------ | ------ | ------ |
|1| project_number | numeric |Project identifier.|
|2| project_title | text |Short description of the project.|
|3| keywords | text |Keywords related to the project.|
|4| authors | numeric |Project identifier.|
|5| start_date | text |Date at which the project starts (dd.mm.yyyy).|
|6| end_date | text |Actual date at which the project ends. Updated if necessary (dd.mm.yyyy).|
|7| university(*) | text | Institution where the project will largely be carried out, based on a list to pick at the moment of the application.|
|8| funding_instrument | text |Research funding scheme as defined by https://www.snf.ch/en/9o5ezhuSlHENVQxr/page/overview-of-funding-schemes|
|9| approved_amount | text |Approved funding amount. Updated if modified. Format is text because not always a number is stored. Ex: it may say "not included in P3".|
|10| discipline_name(*) | text |Discipline name defined by the SNSF. Defined for the main discipline.|
|11| sdg | text | SDG that has been detected, NA if no SDG has been detected in this document by the given system |
|12| system | text | Query system used to detect SDG (see details in Section "SDG detection process") |
|13| hits | numeric | How many hits were produced for a given SDG for the given document by the given system |

(*) These columns are filled out from a drop-down list provided by the SNSF submission system.

### Supporting dataset (`supplementary_data.zip`)

|Column #|Name|Type|Description|Type of entry|
| ------ | ------ | ------ | ------ | ------ |
|1| project_number | numeric |Project identifier.|
|2| funding_instrument_hierarchy | text |Top level hierarchy of the research funding scheme.|
|3| institution | text |Institution where the project will largely be carried out.|Free text|
|4| institution_country(*) | text |The country of the institution. Most international locations are related to mobility fellowships.|
|5| discipline_number(*) | numeric |Discipline ID defined by the SNSF. Defined for the main discipline.|
|6| discipline_name_hierarchy | text |The hierarchy of the main discipline.|
|7| all_disciplines | text |List of all the discipline IDs involved in the project.|
|8| abstract_translated| text |Flag indicating whether the abstract has been translated to English.|Free text|
|9| abstract | text | The scientific abstract of the research project in English, either the original one or the translated one.|

(*) These columns are filled out from a drop-down list provided by the SNSF submission system.

## Code

### Reading and joining in R

```ruby
library(readr)
main_df <- read_csv("sdg_hackathon_data.zip")
additional_df <- read_csv("supplementary_data.zip")

#join on main dataset
library(dplyr)
main_df <- main_df %>% 
  left_join(additional_df)
```

### Reading and joining in python

```python
import pandas as pd

main_df = pd.read_csv("sdg_hackathon_data.zip")
additional_df = pd.read_csv("supplementary_data.zip")

#join on main dataset
main_df = main_df.merge(additional_df, left_on = "project_number", right_on = "project_number", how = "left")
```

## Appendix

### Details on SDG labeling systems

* **Aurora**: These queries were developed by the [Aurora Universities Network](https://aurora-network.global/activity/sustainability/). The Aurora queries were designed to be precise rather than sensitive. To this end, queries have been designed to make use of complex keyword-combinations using several different logical search operators (e.g. 'and', 'or', etc.). [Version 5.0](https://github.com/Aurora-Network-Global/sdg-queries) is used in the 'text2sdg' package.
_All SDGs (1-17) are covered_
* **Elsevier**: A dataset containing the SDG queries developed by [Elsevier](https://www.elsevier.com/connect/sdg-report). The queries are available from [data.mendeley.com](https://data.mendeley.com/datasets/87txkw7khs/1). The Elsevier queries were developed to maximize SDG hits on the [Scopus database](https://dev.elsevier.com/documentation/ScopusSearchAPI.wadl). A detailed description of how each SDG query was developed can be found [here](https://elsevier.digitalcommonsdata.com/datasets/87txkw7khs/1). Version 1 is used in the 'text2sdg' package. 
_SDGs 1-16 are covered (i.e. SDG-17 is not covered)_
* **Ontology**: A dataset containing the SDG queries based on the keyword ontology of Bautista-Puig and Mauleon (2019)[1]. The queries are available from [figshare.com](https://figshare.com/articles/dataset/SDG_ontology/11106113/1). The authors of these queries first created an ontology from selected keywords present in the SDG descriptions and expanded them with keywords from publications from the Web of Science which included the phrases "Millennium Development Goals" and "Sustainable Development Goals".
_All SDGs (1-17) are covered_
* **SDSN**: A dataset containing SDG-specific keywords compiled from several universities from the Sustainable Development Solutions Network (SDSN) Australia, New Zealand & Pacific Network. The authors used UN documents, Google searches and personal communications as sources for keywords.
_All SDGs (1-17) are covered_
* **Siris**: These queries were developed by [SIRIS Academic](http://www.sirislab.com/lab/sdg-research-mapping/). The queries are available from Zenodo.org. The SIRIS queries were developed by extracting key terms from the UN official list of goals, targets and indicators as well from relevant literature around SDGs. The query system has subsequently been expanded with a pre-trained word2vec model and an algorithm that selects related words from Wikipedia.
_SDGs 1-16 are covered (i.e. SDG-17 is not covered)_


### Changes to the P3 database
- All the projects that did not have an abstract were deleted.
- The following columns have been deleted: Project Title English, Responsible Applicant, Lay Summary Lead (English), Lay Summary (English), Lay Summary  Lead (German), Lay Summary (German), Lay Summary Lead (French), Lay Summary (French), Lay Summary Lead (Italian), Lay Summary (Italian), Project Number String, Abstract


### References
[1] Bautista-Puig, N.; Mauleon E. (2019). Unveiling the path towards sustainability: is there a research interest on sustainable goals? In the 17th International Conference on Scientometrics & Informetrics (ISSI 2019), Rome (Italy), Volume II, ISBN 978-88-3381-118-5, p.2770-2771.
