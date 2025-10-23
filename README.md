# Earhquake Monitoring Dashboard for Upstream Oil and Gas Operations in New Mexico, USA

## **Executive Summary:**

Using geospatial data integration, SQL, and model building within ArcGIS Pro, I created an automated public dashboard hosted on ArcGIS Online to review USGS earthquake activity, Salt Water Disposal (SWD) wells, and mapped subsurafce fault systems in relation to the New Mexico Energy, Minerals, and Natural Resources Department's (EMNRD) seismic response protocol.

This tool provides an automated approach to expedite the review process for operators and the NMOCD.


## **Business Problem:**

For operators within oil and gas production areas of New Mexico, it is critical to know immediately if, and when, there is a seismic event that will directly impact operations in order to both follow the NMOCD directives and mitigate production downtime. 
How can we determine the impact of an earthquake event on our water infrastucture (SWD wells and water pipelines)? 
How can we quickly map and list which disposal wells fall within the seismic response areas outlined by the EMNRD?
Can we find trends of where these earthquakes occur to build flexibility into our water disposal infrastructure?


More details on the EMNRD Seismic Response Protocol:
The New Mexico Energy, Minerals, and Natural Resources Department (EMNRD) have put in place a response system for combatting induced seismicity based on magnitude and proximity to active Salt Water Disposal (SWD) wells.
https://www.emnrd.nm.gov/ocd/wp-content/uploads/sites/6/11-23-21-FINAL-NMOCD-Seismic-Mitigation-Protocol-Table.pdf

  Category 1: Two 2.5M events in 30 days result in weekly volume and pressure reporting for SWD wells within a 10 mile radius
 
  Category 2A: Any 3.0M event results in injection volume reductions based on distance to reported epicenter 
  
     - 0-3 miles = 50% reduction 
     - 3-6 miles = 25% reduction
 
  Category 2B: Any 3.5M event results in injection volume reductions based on distance to reported epicenter 
  
     - 0-3 miles = 100% reduction (shut in) 
     - 3-6 miles = 50% reduction 
     - 6-10 miles = 25% reduction

 

## **Methodology:**
1. Query and extract geoJSON URL from USGS Latest Earthquakes Catalog, upload to Power Query for cleaning and transforming data for use in ArcGIS Pro.

2. Import to ArcGIS Pro, QC, and generate multiple ring buffers based on magnitude according to EMNRD protocol.

3. Locate and pull reliable geospatial data sets to include NMOCD Active SWD wells, UT Austin BEG published basement faults clipped to New Mexico, and any other relevant culture data.
   
     Not included in this project but recommend for upstream operator use: 1. In depth SWD details such as formation tops, injection interval, capacity and average usage volume; 2. Internal and third party water pipelines with capacity details (as-built and planned) to better map out water network for field response.

4. Publish relevant information as web layer(s) to ArcGIS Online.
   
5. Configure Map and Dashboard visualizations within ArcGIS Online.
   
6. Generate model within ArcGIS Pro to automate this geoprocessing workflow at a set schedule.

7. Configure automation to refresh geoJSON excel file via task manager.

   
