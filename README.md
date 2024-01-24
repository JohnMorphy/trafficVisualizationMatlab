# Traffic visualizationn built in matlab


## Application features

After starting the application, the user can choose one of three streets. After selecting the route, select the hourly interval and tickrate.

![Przechwytywanie](https://github.com/JohnMorphy/trafficVisualizationMatlab/assets/92916894/59b48d23-1c4a-4b76-8acf-adf8fbd738fb)

After selecting the street, user can generate a visualization.
The "generate visualization" button downloads the data set by the user, populates the slider with selectable time intervals - in the example below, selecting the 60th second analyzes the first 60 seconds of data; selecting 900th second would analyze the last 60 seconds of data.

Then, statistics are downloaded from the API website - for a given road and time period: number of unique cars, average speed in kilometers per hour and total standstill time (sum of time during which the speed was 0 km/h).
The road and the fragment of the road observed by the camera are also drawn. The color of the observed road fragment (camera-coverage) depends on the average speed of the cars.

In case of errors when drawing shp files or downloading data, errors are displayed in the "error messages" section. The errors disappear when you click the "Generate Visualization" button again or change the value on the slider.

![Przechwytywanie2](https://github.com/JohnMorphy/trafficVisualizationMatlab/assets/92916894/d8bba6dc-cab6-459c-848e-5c6e68fc7348)

## Code snippets

### Downloading data via API:

baseURL = 'https://data.transportation.gov/resource/8ect-6jqj.json';
          
           % Selecting location based on the slider
           locationParam = ['location=', app.currentRoad];
          
           % Selecting min and max global time based on the slider
           selectedInterval = 900/app.currentTickrate;
           minGlobalTime = app.currentTime(1) + (app.currentSliderChoice-selectedInterval)*1000;
           maxGlobalTime = minGlobalTime + (selectedInterval*1000);
           timeWhereClause = ['$where=global_time > ', num2str(minGlobalTime), ' AND global_time < ', num2str(maxGlobalTime)];
          
           % Specify the app token if needed
           appToken = 'VNEI5hNy8NgCnI0lmzVtmlCE1';
           options = weboptions('KeyName', 'app_token', 'KeyValue', appToken, 'Timeout', 10);
          
           % Counting unique cars
           countVehiclesURL  = [baseURL, '?$select=count(distinct vehicle_id)&', locationParam, '&', timeWhereClause];

### Plotting the road:

           shpFile = 'US-101.shp';      
           shpFile2 = 'us-101-camera-coverage.shp';
	         shp = shaperead(shpFile);
	         lon = [shp.X];
           lat = [shp.Y];
	         plot(app.UIAxes, lon, lat, '-b', 'LineWidth', 1);

### Getting min and max global time (implementation for us-101):

          Items = ["7:50 - 8:05", "8:05 - 8:20", "8:20 - 8:35"];
          ItemsData = {
                       [1118847000000, 1118847900000];
                       [1118847900000, 1118848800000];
                       [1118848800000, 1118849700000]
                   };
          app.chooseDate_DropDown.Items = Items;
          app.chooseDate_DropDown.ItemsData = ItemsData;


## Used resources:

 - https://data.transportation.gov/Automobiles/Next-Generation-Simulation-NGSIM-Vehicle-Trajector/8ect-6jqj/about_data
   (data source)
 - https://dev.socrata.com/foundry/data.transportation.gov/8ect-6jqj
   (api docs)
 - Matlab: App Designer, Mapping toolbox


