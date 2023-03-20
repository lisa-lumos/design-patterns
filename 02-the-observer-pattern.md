# 2. The Observer Pattern
Suppose you need to build a weather station app where different display elements will be updated based on the WeatherData object, which tracks temperature, humidity, etc. Moreover, the app needs to be expandable: it needs to allow other developers to create their own display elements. 

The app has three components: the physical weather station that measures the weather data, the WeatherData object that tracks the data from the station and updates the display elements, and the 3 display elements that shows the current weather conditions to the user. We need to design an app that uses the WeatherData object to update the displays. 

Whenever the WeatherData object gets updated data from the station, its measurementsChanged() method is called. We need to modify this method so it updates the displays. 

We expect, if the Weather Station is successful, there will be more than three displays in the future, so we create a marketplace for additional displays. Other developers may want to create new custom displays.

## Publishers + Subscribers = Observer Pattern
We call the publisher the Subject, and the subscribers the Observers. 































