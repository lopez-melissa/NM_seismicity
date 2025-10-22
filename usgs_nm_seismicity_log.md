# USGS NM Seismicity Log



#### 1\. USGS EQ QUERY GEOJSON URL	

&nbsp;	catalog website: https://earthquake.usgs.gov/earthquakes/search/#%7B%22currentfeatureid%22%3Anull%2C%22mapposition%22%3A%5B%5B11.22151%2C-165.32227%5D%2C%5B58.21702%2C-24.69727%5D%5D%2C%22autoUpdate%22%3A%5B%22autoUpdate%22%5D%2C%22feed%22%3A%22undefined\_undefined%22%2C%22listFormat%22%3A%22default%22%2C%22restrictListToMap%22%3A%5B%5D%2C%22sort%22%3A%22newest%22%2C%22basemap%22%3A%22grayscale%22%2C%22overlays%22%3A%5B%22plates%22%5D%2C%22distanceUnit%22%3A%22km%22%2C%22timezone%22%3A%22local%22%2C%22viewModes%22%3A%5B%22list%22%2C%22settings%22%2C%22map%22%5D%2C%22event%22%3Anull%2C%22search%22%3Anull%7D



&nbsp;	Custom search for New Mexico state since year 2020

&nbsp;	Mag = 2.5M and greater

&nbsp;	Time Start = 2020-01-01 

&nbsp;	Time End = blank

&nbsp;	Extents: North = 37.00, East = -103.065, South = 32.001, West = -109.046

&nbsp;	Type = Earthquakes

&nbsp;	Output = geoJSON URL

&nbsp;		https://earthquake.usgs.gov/fdsnws/event/1/query.geojson?starttime=2020-01-01%2000:00:00\&maxlatitude=37\&minlatitude=32.001\&maxlongitude=-103.065\&minlongitude=-109.046\&minmagnitude=2.5\&eventtype=earthquake\&orderby=time





&nbsp;	A. Import URL to excel Power Query

&nbsp;		Convert to table

&nbsp;		Expand table contents: type, category, geometry, coordinates

&nbsp;		Split coordinate column by comma delimiter resulted in long, lat, depth.  Labeled as such

&nbsp;		convert time from ms since 1970 to date-time format

&nbsp;			add custom column: "date.time"

&nbsp;			custom formula: '= @time / 86400000 + 25569

&nbsp;			format to date/time

&nbsp;		Saved to 'C:\\Users\\lopez\\OneDrive\\Documents\\Melissa\\Google Data Analytics Cert\\Tableau\\datasets\\usgs\_nm\_eg.xlsx'

&nbsp;		\*\*idea is to refresh to pull latest events from USGS\*\*



&nbsp;	B. Import to ArcGIS / Tableau / etc as WGS84

&nbsp;		C:\\Users\\lopez\\OneDrive\\Documents\\ArcGIS\\Projects\\NM\_Seismicity\\NM\_Seismicity.aprx



&nbsp;		Reminder - To buffer distance, convert to StatePlane coordinate reference system: NAD 83 State Plane NM East 2257 



&nbsp;		\*\*Keeping this script documented, but using power query to classify events before being brought into ArcGIS. 

&nbsp;		In ArcGIS pro, calculate a category field using python.

&nbsp;		New Field "nmocd\_cat"

&nbsp;			nmocd\_cat = classify\_mag(!mag!)

&nbsp;			Code:

&nbsp;			def classify\_mag(mag):

&nbsp;  			 return (

&nbsp;     			  'cat\_1' if 2.5 <= mag < 3.0 else

&nbsp;     			  'cat\_2a' if 3.0 <= mag < 3.5 else

&nbsp;       		  'cat\_2b'

&nbsp; 			  )



#### 2\. Generate Buffer Distances based on magnitude according to NMOCD regulations

&nbsp;	https://www.emnrd.nm.gov/ocd/wp-content/uploads/sites/6/11-23-21-FINAL-NMOCD-Seismic-Mitigation-Protocol-Table.pdf



&nbsp;	- Category 1: Two 2.5M events in 30 days result in weekly volume reporting for SWD wells within 10 mile radius

&nbsp;	- Category 2a: Any 3.0M event results in inj volume reductions based on distance to reported epicenter

&nbsp;		0-3 miles = 50% reduction; 3-6 miles = 25% reduction

&nbsp;	- Category 2b: Any 3.5M event results in inj volume reductions based on distance to reported epicenter

&nbsp;		0-3 miles = 100% reduction (shut in); 3-6 miles = 50% reduction; 6-10 miles = 25% reduction





&nbsp;	Method for buffers: 

&nbsp;		Select by attribute: 

&nbsp;			Cat 2b: where mag is greater than or equal to 3.5 (19 selected); 

&nbsp;				hit apply and drag layer to input of Multi ring buffer tool

&nbsp;				Set 3, 6, 10 mile distances with overlapping (disks), geodesic method

&nbsp;			Cat 2a: where mag is greater than or equal to 3.0 AND less than 3.5  (59 selected)

&nbsp;				hit apply and drag layer to input of Multi ring buffer tool

&nbsp;				Set 3, 6 mile distances with overlapping (disks), geodesic method

&nbsp;			Cat 1:  mag < 3 And date\_time >= CURRENT\_DATE() - INTERVAL '30' DAY (5 selected)

&nbsp;				hit apply and drag layer to input of Multi ring buffer tool

&nbsp;				Set 10 mile distances with overlapping (disks), geodesic method



&nbsp;		Three individual layers with buffers should be displayed

&nbsp;	

&nbsp;		Merge all three layers into one named 'sra\_buffers' using Merge Data Management tool. This also preserves attribute tables

&nbsp;			Remove individual cat buffers from contents

&nbsp;		

&nbsp;

&nbsp;	Built Model within ArcGIS pro to run and publish updates daily at 5:00 AM, after the excel file is refreshed.  

&nbsp;	Can increase frequency if needed.

&nbsp;	Exported as python file in the following location: 

&nbsp;		C:\\Users\\lopez\\OneDrive\\Documents\\Melissa\\Google Data Analytics Cert\\Tableau\\eq\\arcgis model

&nbsp;	



&nbsp;	Hosted Feature layer: https://melgeosciences.maps.arcgis.com/home/item.html?id=9003b1292a4244609efd1b2e3475095d





#### 3\. Incorporate geospatial data

#### &nbsp;  NMOCD Active SWD Wells 

&nbsp;	If not public, further details on injection intervals and volumes are important to know for analytics

&nbsp;	Otherwise, display all active SWD wells

&nbsp;	

&nbsp;	Extract well details for wells that fall within buffers with a listed distance and category classification

&nbsp;	source: https://ocd-hub-nm-emnrd.hub.arcgis.com/datasets/387f397ebf164c7aa6a752aac7d22b17\_0/explore?filters=eyJ0eXBlIjpbIlNhbHQgV2F0ZXIgRGlzcG9zYWwiLCJJbmplY3Rpb24iXSwic3RhdHVzIjpbIkFjdGl2ZSJdfQ%3D%3D\&location=32.677782%2C-104.297618%2C10.99



&nbsp;	In dashboard (Step 5): Set up the lasso selection tool in ArcGIS dashboard to draw polygon around SWD wells within SRA Buffers.  This then filters the table to include selected wells.



&nbsp;	Hosted Feature layer: https://melgeosciences.maps.arcgis.com/home/item.html?id=eacfacf1351443dfa0f6ec9bc4ef0b5c



#### &nbsp;  BEG Deep Faults for NM - limited to Permian basin

&nbsp;	This data was downloaded from the University of Texas at Austin, Bureau of Economic Geology Report of Investigations No. 286: The Geologic Basement of Texas: A Volume in Honor of Peter T. Flawn. The repository contains a zipped folder containing Geographic Information System (GIS) shapefiles for the subsurface fault traces 	that were interpreted by the authors following the methods described in the manuscript.



&nbsp;	source: https://dataverse.tdl.org/dataset.xhtml?persistentId=doi:10.18738/T8/UHOUX8

&nbsp;	

&nbsp;	Hosted feature layer: https://melgeosciences.maps.arcgis.com/home/item.html?id=5765a277650f406a83e15b3181244674



#### &nbsp;  State Land Office Geologic Regions

&nbsp;	

&nbsp;	Hosted feature layer: https://melgeosciences.maps.arcgis.com/home/item.html?id=695a8babf2bf4cd99185373104ac5f91



#### &nbsp;  As-Built Water Pipelines (Optional for public dashboard)

&nbsp;	I omitted this data due to lack of reliable public data. 



&nbsp;	Future update: Dig into the following resources to find as-built water pipelines for public use.



&nbsp;	https://ocd-hub-nm-emnrd.hub.arcgis.com/maps/8647a25ac577430987fb8795d34b74d5/about

&nbsp;	https://pvnpms.phmsa.dot.gov/PublicViewer/

&nbsp;	https://dpgnm.com/national\_pipeline\_mapping/





#### 4\. Publish layers to ArcGIS Online

&nbsp;	Share as web layer via ArcGIS Pro

&nbsp;	Publish Web Layer of usgs\_nm\_eg and sra\_buffers

 			Configure to Editing and Preserve editor tracking then set Time Zone

 			Open Map Properties to Allow assignment of unique numeric IDs



&nbsp;	Hosted Feature layer: https://melgeosciences.maps.arcgis.com/home/item.html?id=9003b1292a4244609efd1b2e3475095d





#### 5\. Build out ArcGIS Online Map and Dashboard





#### 6\. Generate automated geoprocessing model with Model Builder in ArcGIS Pro

&nbsp;	Built Model within ArcGIS pro to run and publish updates daily at 5:00 AM, after the excel file is refreshed.

 	Can increase frequency if needed.

 	Exported as python file in the following location:

 		C:\\Users\\lopez\\OneDrive\\Documents\\Melissa\\Google Data Analytics Cert\\Tableau\\eq\\arcgis model







#### 7\. Configure automation to refresh usgs\_nm\_eq.xlsx via task manager 

&nbsp;		

&nbsp;	A. Set up the macro within excel - new workbook named usgs\_nm\_eq\_vba.xlm

&nbsp;		macro named usgs\_refresh\_geoJSON

&nbsp;		

&nbsp;	B. Set up task manager	

&nbsp;		task names 'usgs\_refresh\_geoJSON' and is set to perform daily at 4:00 AM until 10/20/2026

&nbsp;		

















&nbsp;	

