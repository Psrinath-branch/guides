
# AA Custom Fraud Analysis Guide

- Author : Bohyung Son
- Email : bson@branch.io
- Last updated: Dec. 27 2018

---
## Table Of Content
* Custom Fraud Category
* Data points and lag-times

---
## Custom Fraud Category

#### Normal
* Devices currently regarded as normal device.
* Fraud detection logic is keep being improved, so more devices would be categorized as suspicious or fraudulent in the next analysis.

#### Custom Fraud Rule (install only)
* Installs marked as suspicious by AA custom fraud rule which was enabled by advertiser.

#### Country Conflict (install only)
* Installs marked as suspicious by AA country conflict fraud rule which was enabled by advertiser.

#### CI:-100~0 (install only)
* Installs attributed to the publishers by click injection fraud.
* Logic is based on the TCTDIT which is lag-time calculation of the timestamp of the matched click and the timestamp of the app is installed on the device.
* Positive value means app is installed on the device after the matched click, and negative value means app is installed on the device and then the matched click is fired.
* Click injecting fraudsters install malware's on devices in advance, and fire clicks as soon as new app installation is detected.
   By doing so, the injected click can steal credit for the install as a last click before first open.

#### Emul
* Devices recognized as either emulator or custom ROM or fraud tool via various data points.
* May small amount of normal devices could be included in this category, but currently identifying each device conservatively, so showing relatively high accuracy.

#### SusDevice
* Devices recognized as suspicious based on many data points like country, brand, model, carrier, language, and etc.
* This is category for indicative information, and does not mean each device in this category is fraud.
* It is recommended to compare volume devices in this category across publishers and
     setting baseline using internal advertising source and trusted publishers.

#### FP:TCTTIT>1H
* First of all, it is strongly recommended to set fingerprint window to 1 hour for all the publishers.
* Among fingerprint installs click to install lag-time is greater than 1 hour.
* This is a characteristic grouping of installs, and each install fall into this category does not mean fraud.
     For normal publishers volume of installs fall into this category is usually less than 10~5%.
     And recommend to set baseline using internal advertising source and trusted publishers.

#### FP:CS Suspicious
* Among fingerprint installs which are suspicious as click spamming.
* This is a characteristic grouping of installs, and each install fall into this category does not mean fraud.
     For normal publishers volume of installs fall into this category is usually less than 10~5%.
     And recommend to set baseline using internal advertising source and trusted publishers.
* If volume of both "FP:TCTTIT>1H" and "FP:CS Suspicious" are high more likely of click spamming/flooding.

#### GDT:Null
* Installs install begin timestamp (from Google Play Install Referrer Library) is not collected.
* It means Google Play Store app is not properly updated for at least 1 year, and it indicate that more likely of not a normal user's device.

#### NOTE:
1. If volume of each category is less than 10~5% per publisher and sub-publisher, we can regard it as normal.
    But we also need to set baseline using internal advertising source and trusted publisher, and compare across publishers.
2. Apps in some verticals could have tendency of some fraud category is higher than normal cases.
    Ex1) Travel app: more likely hood of more country conflict.
    Ex2) MMORPG games: Some hard-core gamers would use emulator to play on desktop/laptop.

---
## Data points and lag-times

![image](https://github.com/bson-branch/guides/blob/master/images/dp_and_lags.png?raw=true)

#### Datapoint (Field)
1. TIT (TMC Install Timestamp): The time app is first opened
 * "Install Log Report" column name: Datetime
 * Reporting API/Install Log field name: created


2. DIT (Device Install Timestamp) : The time app is installed on the device.
 * "Install Log Report" column name: Install Datetime
 * Reporting API/Install Log field name: install_date


3. TCT (TMC Click Timestamp) : Timestamp of matched click
 * "Install Log Report" column name: Click/Impression Datetime
 * Reporting API/Install Log field name: session_datetime


4. TET (TMC Event Timestamp) : The time event is sent.
 * "Event Log Report" column name: Datetime
 * Reporting API/Event Log field name: created


#### Lag-time
1. TCTTIT : Lag-time between TCT and TIT
 > Formula: TIT - TCT

2. TCTDIT : Lag-time between TCT and DIT
 > Formula: DIT - TCT

 * Most of normal cases, this value is positive.
 Negative value does not always mean fraudulent install, and small volume of normal installs could fall into this category, so need to see and compare lag-time pattern across publishers.

 * For click injected installs, this value is between -100 and 0.
        May some normal installs could fall into between -100 and 0, but the amount should be a small faction.
        For click injecting publishers and sub-publishers, high percentage of installs are falled into this category.

3. TITTET : Lag-time between TIT and TET.
 > Formula: TET - TIT

 * This lag-time is used to analyze user's post-install behavioral pattern.
