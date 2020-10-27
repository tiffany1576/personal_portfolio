
---
 ## <b>MEASURING THE IMPACT OF DOJ FUNDING IN THE OPIOID EPIDEMIC</b>

### Table of Contents
1. [Background](#background)
2. [Problem Statement and Goal](#goal)
3. [Data Acquisition](#acquisition)
4. [Data Cleaning](#cleaning)
5. [Models](#models)
6. [Outcomes](#outcomes)
7. [Conclusions](#conclusion)
8. [Next Steps](#next)


### Background <a id = 'background'></a>
The modern opioid epidemic in America kicked off in the 1990s, with the introduction of Purdue Pharmaceuticals' new extended-release oxycodone formula, Oxycontin, in 1996. Purdue heavily marketed Oxycontin as a safe, effective and generally less-addictive treatment for long-term chronic pain, and sales ballooned from $48 million in 1996 to almost $1.1 billion in 2000. Other pharmaceutical manufacturers soon followed with their synthetic opioid formulas and novel delivery systems- namely, topical, transdermal, buccal and sublingual applications containing fentanyl. 

The Centers for Disease Control (CDC) categorizes the opioid epidemic in 3 "waves", beginning with the increase in prescription opioid use in the 1990s. By 2010, heroin overdose deaths started to rise, marking the beginning of the second wave. In 2013, fentanyl and other synthethic opioid overdoses exploded, and synthetic opioids are still the most common type of opioid found in a deceased overdose victim today.

In 2003, representative Harold Rogers (R-KY), sponsored legislation to make federal funds available for states to launch Prescription Drug Monitoring Programs (PDMP). These PDMP platforms track the prescription and dispensation of controlled substances within each state.  By 2016, 49 states and 1 US territory had an operational PDMP. In 2017, an additional $600,000 was awarded to Kentucky for educating prescribers and pharmacists on how to use the data available through Kentucky's PDMP platform, KASPER. During this time, interstate sharing of PDMP information expanded, and by 2018, all but 3 states participated in sharing data across state lines. 

Kentucky is one of the states at the epicenter of the opioid public health crisis.  Testimony before the US House Subcommittee on Economic Development, Public Buildings, and Emergency Management of the Committe on Transportation and Infrastructure on December 12, 2017 highlighted the pervasiveness and ubiquity of opioid abuse in Appalachia. On October 25, 2018, the US DOJ announced the formation of the Appalachian Regional Prescription Opioid Strike Force (ARPO Strike Force) in cooperation with the US Attorney's offices in Kentucky, Tennessee, West Virginia, Ohio and Alabama. In 2019, the ARPO Strike Force announced the arrests of several dozen medical professionals who had allegedly participated in the prescribing and distribution of opioids in the region. 

Notably, Kentucky is also one of the 34 states that elected to expand their Medicaid and CHIP program with the Affordable Healthcare Act. According to CMS/Medicaid.gov, Kentucky has enrolled over 1.4 million individuals as of June 2020. Medicaid prescription data is publicly available, and can be used to evaluate the impact of opioid interdiction efforts.

Sources: 

[Tracing the US opioid crisis to its roots](https://www.nature.com/articles/d41586-019-02686-2)

[Understanding the Epidemic](https://www.cdc.gov/drugoverdose/epidemic/index.html)

[The Fentanyl Story](https://www.jpain.org/article/S1526-5900(14)00905-5/pdf)

[THE OPIOID EPIDEMIC IN APPALACHIA: ADDRESSING HURDLES TO ECONOMIC DEVELOPMENT IN THE REGION](https://www.govinfo.gov/content/pkg/CHRG-115hhrg29961/html/CHRG-115hhrg29961.htm)

[Appalachian Regional Prescription Opioid (ARPO) Strike Force Takedown Results in Charges Against 60 Individuals, Including 53 Medical Professionals](https://www.justice.gov/opa/pr/appalachian-regional-prescription-opioid-arpo-strike-force-takedown-results-charges-against)

[Second Appalachian Region Prescription Opioid Strikeforce Takedown Results in Charges Against 13 Individuals, Including 11 Physicians](https://www.justice.gov/opa/pr/second-appalachian-region-prescription-opioid-strikeforce-takedown-results-charges-against-13)

[Harold Rogers Prescription Drug Monitoring Program Funding to Boost Drug Abuse Fight in Kentucky](https://halrogers.house.gov/press-releases?ID=6377EF8F-F7E1-49A8-B6F3-9774485C8B3F)

[The Evolution of the PDMP](https://www.pharmacytimes.com/contributor/marilyn-bulloch-pharmd-bcps/2018/07/the-evolution-of-the-pdmp)

[Medicaid & CHIP in Kentucky](https://www.medicaid.gov/state-overviews/stateprofile.html?state=kentucky)


[Commonwealth of Kentucky Section 1115 Substance Use Disorder (SUD) Demonstration Implementation Plan](https://www.medicaid.gov/Medicaid-CHIP-Program-Information/By-Topics/Waivers/1115/downloads/ky/health/ky-health-sud-implement-protocol-apprvl-10052018.pdf)


### Problem Statement and Goal <a id = 'goal'></a>
Using historical prescription data from Medicaid, create forecasted outcomes for opioid prescriptions in 2018, 2019, and 2020. Compare the forecasts to the actual prescription information. Key events, legislation and funding from 2017, 2018 and 2019 should reflect in the actual prescription information being less than the predicted values. 

Has the emphasis on substance use disorder prevention and treatment, in concert with additional funding and DOJ resources, resulted in a significant decline in the amount of opioid medications dispensed in the Commonwealth of KY?



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

I used the list of drugs from the FDA opioid data to flag transactions in the RX data if the product_FDA_name contained any of the names of these opioid medications. 

In this way, I have cross mapped opioid prescriptions using both name and NDC. 

I created several different slices of data, but the most useful dataframe is a timeseries that includes DEA schedule, whether or not the drug is used for MAT, and dispensations per 1000 Medicaid enrollees.

Enrollment data was averaged to obtain the average enrollment within a quarter to correspond with the quarterly prescription data. Units reimbursed is a rough approximation of doses dispensed. To account for population shifts, I created a Units_per_1000 field that is the quarterly volume of dispensed doses divided by the average enrollment.



### Models <a id = 'models'></a>

SARIMA/ARIMA with and without differentiation. 


### Outcomes<a id = 'outcomes'></a>
Gridsearch using PMDARIMA. Both the MAT drug dispensations and non MAT drug dispensations exhibited a little seasonality, and both required two levels of differentiation for the highest accuracy using AIC testing.
Best parameters for non MAT :  ARIMA(1,2,1)(1,1,0)[4]  
Best parameters for MAT: Best model:  ARIMA(0,2,1)(2,1,0)[4]
Interpret the parameters as cfg = [(p,d,q), (P,D,Q)[m], where p: Trend autoregression order. d: Trend difference order. q: Trend moving average order.
P: Seasonal autoregressive order. D: Seasonal difference order. Q: Seasonal moving average order. m: The number of time steps for a single seasonal period.

Once the SARIMA models are fit to their respective data sets, used a sliding window predictor with cross-validation. I selected 4 data points per window, 1 step at a time, and predicted 4 events in the future (use a year of data to predict a year of observations).

These predictions are then compared to the actual values for the same time frame to answer the main question-- are these measures working to slow the flow of opioids?


### Conclusions<a id = 'conclusion'></a>

Broadly speaking, overall opioid doses per 1000 are decreasing. However, this trend observation on this particular dataset isn't necessarily a bellwether indicator of success. 

The data did not include strength per dose, which is used to determine Medical Morphine Equivalents, a standard of potency used to compare opioid medications. Physicians and their patients can choose from a wide variety of opioids of differing potencies. In other words, a physician may have decreased the number of doses prescribed to a patient, but may have increased the strength, resulting in a net (or perhaps greater) Medical Morphine Equivalency for the patient.

Drugs used for the treatment of Substance Use Disorder are seeing a gradual increase in dispensation. Again, there are many other variables that factor into whether a patient participates in medication-assisted substance use disorder treatment. However, the increase in actual doses fell short of the predicted values. Overall, based on the data and models here, the Commonwealth's efforts to increase access and use of MAT for SUD have not been successful over the past couple of years.

### Next Steps<a id = 'next'></a>

The data, while clean, was not particularly robust. I think a monthly timeseries as opposed to quarterly would provide more accurate outcomes. 

Other opportunities include using data from other states within Appalachia to look at trends from a regional perspective.

Additionally, I plan to incorporate FBI crime data and census data, to look for other potential predictors and social determinants of health that may identify at-risk communities in need of more local treatment options and wraparound behavioral services. 






