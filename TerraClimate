/* 
This program, created by us, accesses the entire TerraClimate dataset. 
It making possible to acquire various climatic information for land surfaces all over the planet. 
Please, learn more about the TerraClimate dataset here: 
1 - https://www.climatologylab.org/terraclimate.html
2 - https://repositorio.unesp.br/bitstream/handle/11449/194259/magnoni_phj_me_botfca.pdf?sequence=5&isAllowed=y */


// Start it!

// Define your Region of Interest (ROI):
var roi = table.filter(ee.Filter.eq('NAME', 'Chapadão do São Francisco'));


// Add the ROI on the map:
Map.addLayer(roi, {},'Limites da Área em Estudo', false); // Use false to avoid plotting the geometry' ROI.


// Center the ROI on the map (Ex.: 5 zoom):
Map.centerObject(roi, 5);


// Create a function to clip the dataset objects from the ROI:
var clipToCol = function(image){
  return image.clip(roi)};


// Access the dataset and apply filters to it:
var collection = ee.ImageCollection('IDAHO_EPSCOR/TERRACLIMATE') // The variable called 'collection' stores the address of the dataset.
                  .filter(ee.Filter.date('2000-01-01', '2022-12-31')) // Apply a temporal filter.
                  .map(clipToCol); // Apply the function defined above and cut out the dataset.


// Print on the console how many images were found within the filtered dataset:
print('How many images were found on TerraClimate?', collection.size());


// Print the filtered dataset information on console according to what you have defined:
print('TerraClimate Dataset Filtered:', collection); // Look that, there is one image for each date within the time window.


// Okay, it work well. Now, specify the climate information of interest:
var information = collection.select('pr'); // Look that, the information I'm specifying is called 'pr'. In this dataset, pr is precipitation.


// Print the information from the dataset filtered by 'pr' on the console:
print('TerraClimate Dataset Filtered by [pr]:', information); // Look that, there is one image 'pr' for each date within the time window.


// Define a color palette to be associated with the pixel values, it can help the visualization and interpretation:
var informationVis = {
  min: 14293,
  max: 38177,
  palette: ['ab0000', 'ff0000', 'ca531a', 'ff7c1f', 'ffa12d', 'ffc969', 'ffe6a2', 'fdffb4', 
  'e5f9ff', 'caebff', 'acd1ff', '8dbae9', '5699ff', '2955bc', '1a3678', '#142a5e']};


// Get the accumulated rainfall in the time window you set:
var accumulated = information.sum();


// Add the accumulated 'pr' to the map, taking into account the previously defined time window:
Map.addLayer(accumulated, informationVis, 'Accumulated pr');


// Ok, it work well. Now, choose and add an image from the dataset to the map, considering a specific date of interest:
var specificimage = ee.Image('IDAHO_EPSCOR/TERRACLIMATE/201001').select('pr').clip(roi);


// Access and add on the console the Image metadate:
print('Specific image properties:', specificimage);


// Define a color palette to be associated with the pixel values of accumulated pr specific image:
var informationVisspcifcimage = {
  min: 10,
  max: 287,
  palette: ['ab0000', 'ff0000', 'ca531a', 'ff7c1f', 'ffa12d', 'ffc969', 'ffe6a2', 'fdffb4', 
  'e5f9ff', 'caebff', 'acd1ff', '8dbae9', '5699ff', '2955bc', '1a3678', '#142a5e']};
  

// Add the accumulated pr specific image on the map:
Map.addLayer(specificimage, informationVisspcifcimage,'Accumulated pr specific image');


// Get the metadate of each object of dataset:
var PR = information.map(function(img){
var date = img.get('system:time_start');
return img.set('system_time_start', date);
});


// Define the attributes to later create the chart:
var createTS = function(img){
var date = img.get('system_time_start');
var value = img.reduceRegion(ee.Reducer.sum(), roi).get('pr');
var ft = ee.Feature(null, {'system:time_start': date, 
                             'date': ee.Date(date).format('Y/M/d'), 
                             'value': value});
                               return ft};


// Create the chart:
var PR_final = PR.map(createTS);
var graph = ui.Chart.feature.byFeature(PR_final, 'system:time_start', 'value');
print(graph.setChartType("ColumnChart")
           .setOptions({title: 'Cumulative Rainfall',
                        vAxis: {title: 'mm'},
                        hAxis: {title: 'Date'}}));


// Export the image:
Export.image.toDrive({
  image: specificimage, 
  description:'PR_01_2000', 
  scale:4000,
  region:roi,
  crs: 'EPSG:31983',
  maxPixels:1e19,
  folder: 'PR TerraClimate'});

// Finish it!



//=========================================================================================================================================================
/* 
This second program has been created to access the ERA5-Land dataset. 
One more time, it making possible to acquire various climatic information for land surfaces all over the planet. 
Please, learn more about the ERA5-Land dataset here: 
1 - https://www.ecmwf.int/
2 - https://developers.google.com/earth-engine/datasets/catalog/ECMWF_ERA5_LAND_MONTHLY_AGGR */


// Start it!

// First, let's create a function to acess the 'temperature_2m' data and than convert it to °C:
var temperatur_in_C = function(img){
  var bands = img.select('temperature_2m').clip(roi).subtract(273.15);
  return bands.rename('Temperature')
  .copyProperties(img,['system:time_start','system:time_end'])};


// Access the dataset and apply filters to it:
var collection_ERA5 = ee.ImageCollection("ECMWF/ERA5_LAND/MONTHLY_AGGR") // The variable called 'collection_ERA5' stores the address of the dataset.
                  .filter(ee.Filter.date('2000-01-01', '2022-12-31')) // Apply a temporal filter.
                  .map(temperatur_in_C); // Apply the function defined above and cut out the dataset.


// Print on the console how many images were found within the filtered dataset:
print('How many images were found on ERA5?', collection_ERA5.size());


// Print the filtered dataset information on console according to what you have defined:
print('ERA5 Dataset Filtered:', collection_ERA5); // Look that, there is one image for each date within the time window.


// Create the chart: 
print(
  ui.Chart.image.series(collection_ERA5, roi, ee.Reducer.mean(), 3000, 'system:time_start') // Here, it is so important note that I am using the '.mean' reducer.
  .setChartType('LineChart')
  .setOptions({
    title: 'Mean 2 m Air Temperature',
    vAxis: {title: '°C'},
    hAxis: {title: 'Date'},
    series: {0: {color: 'red'}}
  })
);


// Get the Mean 2 m Air Temperature in the time window you set:
var airtemeperature = collection_ERA5.mean();


// Define a color palette to be associated with the pixel values of air temeperature image:
var informationVisairtemeperature = {
  min: 21,
  max: 26,
  palette: ['ab0000', 'ff0000', 'ca531a', 'ff7c1f', 'ffa12d', 'ffc969', 'ffe6a2', 'fdffb4', 
  'e5f9ff', 'caebff', 'acd1ff', '8dbae9', '5699ff', '2955bc', '1a3678', '#142a5e']};
  

// Add the the Mean 2 m Air Temperature to the map, taking into account the previously defined time window:
Map.addLayer(airtemeperature, informationVisairtemeperature, 'Air Temperature °C');


// Now, choose and add an image from the dataset to the map, considering a specific date of interest:
var specificimageairtemperature = ee.Image('ECMWF/ERA5_LAND/MONTHLY_AGGR/200001').select('temperature_2m').clip(roi).subtract(273.15);


// Add the air temperature °C specific image on the map:
Map.addLayer(specificimageairtemperature, informationVisairtemeperature, 'Air temperature °C on specific image');



// Export the image:
Export.image.toDrive({
  image: airtemeperature, 
  description:'AIRTC_01_2000', 
  scale:3000,
  region:roi,
  crs: 'EPSG:31983',
  maxPixels:1e19,
  folder: 'AIRTC ERA5'});

// Finish it!
// Thank you.
