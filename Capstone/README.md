
---
 ## <b>MEASURING THE IMPACT OF DOJ FUNDING IN THE OPIOID EPIDEMIC

### Table of Contents
1. [Background](#background)
2. [Problem Statement and Goal](#goal)
3. [Data Acquisition](#acquisition)
4. [Data Cleaning](#cleaning)
5. [Models](#models)
6. [Outcomes](#outcomes)
7. [Conclusions](#conclusion)


### Background <a id = 'background'></a>
The modern opioid epidemic in America kicked off in the 1990s, with the introduction of Purdue Pharmaceuticals' new extended-release oxycodone formula, Oxycontin, in 1996. Purdue heavily marketed Oxycontin as a safe, effective and generally less-addictive treatment for long-term chronic pain, and sales ballooned from $48 million in 1996 to almost $1.1 billion in 2000. Other pharmaceutical manufacturers soon followed with their synthetic opioid formulas and novel delivery systems- namely, topical, transdermal, buccal and sublingual applications containing fentanyl. 

The Centers for Disease Control (CDC) categorizes the opioid epidemic in 3 "waves", beginning with the increase in prescription opioid use in the 1990s. By 2010, heroin overdose deaths started to rise, marking the beginning of the second wave. In 2013, fentanyl and other synthethic opioid overdoses exploded, and synthetic opioids are still the most common type of opioid found in a deceased overdose victim today.

In 2003, representative Harold Rogers (R-KY), sponsored legislation to make federal funds available for states to launch Prescription Drug Monitoring Programs (PDMP). These PDMP platforms track the prescription and dispensation of controlled substances within each state.  By 2016, 49 states and 1 US territory had an operational PDMP. In 2017, an additional $600,000 was awarded to Kentucky for educating prescribers and pharmacists on how to use the data available through Kentucky's PDMP platform, KASPER. During this time, interstate sharing of PDMP information expanded, and by 2018, all but 3 states participated in sharing data across state lines. 

Kentucky is one of the states at the epicenter of the opioid public health crisis.  Testimony before the US House Subcommittee on Economic Development, Public Buildings, and Emergency Management of the Committe on Transportation and Infrastructure on December 12, 2017 highlighted the pervasiveness and ubiquity of opioid abuse in Appalachia. On October 25, 2018, the US DOJ announced the formation of the Appalachian Regional Prescription Opioid Strike Force (ARPO Strike Force) in cooperation with the US Attorney's offices in Kentucky, Tennessee, West Virginia, Ohio and Alabama. In 2019, the ARPO Strike Force announced the arrests of several dozen medical professionals who had allegedly participated in the prescribing and distribution of opioids in the region. 

Notably, Kentucky is also one of the 34 states that elected to expand their Medicaid and CHIP program with the Affordable Healthcare Act. According to CMS/Medicaid.gov, Kentucky has enrolled over 1.4 million individuals as of June 2020. Medicaid prescription data is publicly available, and can be used to evaluate the impact of opioid interdiction efforts.






Sources: 
https://www.nature.com/articles/d41586-019-02686-2
https://www.cdc.gov/drugoverdose/epidemic/index.html
https://www.jpain.org/article/S1526-5900(14)00905-5/pdf
https://www.govinfo.gov/content/pkg/CHRG-115hhrg29961/html/CHRG-115hhrg29961.htm
https://www.justice.gov/opa/pr/appalachian-regional-prescription-opioid-arpo-strike-force-takedown-results-charges-against
https://www.justice.gov/opa/pr/second-appalachian-region-prescription-opioid-strikeforce-takedown-results-charges-against-13
https://halrogers.house.gov/press-releases?ID=6377EF8F-F7E1-49A8-B6F3-9774485C8B3F
https://www.pharmacytimes.com/contributor/marilyn-bulloch-pharmd-bcps/2018/07/the-evolution-of-the-pdmp
https://www.medicaid.gov/state-overviews/stateprofile.html?state=kentucky

More Statistics: https://www.medicaid.gov/Medicaid-CHIP-Program-Information/By-Topics/Waivers/1115/downloads/ky/health/ky-health-sud-implement-protocol-apprvl-10052018.pdf

### Problem Statement and Goal <a id = 'goal'></a>
Using historical prescription data from Medicaid, create forecasted outcomes for opioid prescriptions in 2018, 2019, and 2020. Compare the forecasts to the actual prescription information. Key events, legislation and funding from 2017, 2018 and 2019 should reflect in the actual prescription information being less than the predicted values. 
### Data Acquisition <a id = 'acquisition'></a>
Key data elements:
1. Using the OpenData API and SodaPy, acquire Kentucky Medicaid Prescription information
2. Using the OpenFDA API, acquire the FDA full drug listing and National Drug Codes. These NDCs will be used to identify the opioid prescriptions in the Medicaid dataset
3. Using the OpenData API and SodaPy, acquire Kentucky Medicaid Enrollment Infomration through June 2019. Supplement this data with data taken from each of the monthly reports provided by the [Kentucky Cabinet for Health and Family Services](https://chfs.ky.gov/agencies/dms/dafm/Pages/statistics.aspx)


### Data Cleaning and Engineering <a id = 'cleaning'></a>
The Medicaid prescription data includes rows where the drug name and NDC code are null. This can happen when certain biologics are administered or there is corruption in the data. For the purposes of this evaluation, all rows with nulls in the prescription_name field are dropped.

A note about NDC codes:

From FDA.gov : The NDC will be in one of the following configurations: 4-4-2, 5-3-2, or 5-4-1.    

The Medicaid prescription data formats all NDC codes into a 5-digit labeler code and 4-digit product code. These are then concatenated as labeler + product to produce a 9-digit NDC. 

In order to use the NDC as a key for both dataframes, FDA codes in 4-4-2 format are converted to 5-4 format by adding a leading zero and codes in 5-3-2 format are converted to 5-4 format by adding a leading zero to the second portion of the NDC. The digits to the right of the second hyphen are dropped for the purposes of this evaluation, as the prescription data only includes the first two sections of the NDC (labeler and product codes).

Additionally, NDC codes can expire. When looking at historical prescription data, it is possible to miss some opioids if matching on NDC codes only. The data from the FDA is updated daily, and expired codes are purged during updates. 

I used the list of drugs from the FDA opioid data to create a list of drug names to then flag transactions in the RX data if the product_FDA_name contained any of the names of these opioid medications. 

In this way, I have cross mapped opioid rx using both name and NDC. 

With cleaning, I have one dataframe, merged_df, that contains all the prescription data and whether or not the prescription is for an opioid and if so, is it for an opioid used in substance abuse disorder treatment?


Enrollment data was sourced from both CMS and the KY Cabinet for Health and Family Services. 

### Models <a id = 'models'></a>


### Outcomes<a id = 'outcomes'></a>



### Conclusions<a id = 'conclusion'></a>







