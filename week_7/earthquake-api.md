##### The brief
Create a react component that displays a list of places where earthquakes have happened.
List should be filterable by magnitude or magtype of the earthquake.
If the filter does not return any items then display a message to say ‘not found’.
Do not worry about resetting the filters or styling.
Pay attention to reusing and composing components where possible.
Use the following endpoint: https://earthquake.usgs.gov/fdsnws/event/1/query?format=geojson&starttime=2014-01-01&endtime=2014-01-02
The list items should display in the following format
Id: place: mag: magType
e.g.
- Ak10992887: 117km NW of Talkeetna, Alaska : 1.1 : ml
- Nc72134401 : 6km E of Mammoth Lakes, California : 0.6 : md
Create two inputs with buttons:
Text Input with button to filter by magnitude
Text Input with button to filter by magtype
 (edited) 





1:15
https://earthquake.usgs.gov/fdsnws/event/1/query?format=geojson&starttime=2014-01-01&endtime=2014-01-02