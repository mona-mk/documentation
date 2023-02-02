# Bokeh
Bokeh is a third party python library. It is written in Python but there are versions of the library written in other languages R, Lua.
You write Python code and Python converts that code into HTML, Javascript and CSS which is displayed in the browser in the form of `D3.js` graphs (so these are modern graphs not just JPG graphs).
There is also Bokeh server module inside Bokeh library. Bokeh server lets you serve powerful interactive graphs where you can embed bokeh widgets like buttons, drop-down lists, text inputs etc. These widgets allow the user to interact with a graph and do things like filtering data, label graph features dynamically, change values, etc. With bokeh server, you can stream data so you can plot in real time.

---
**Bokeh server**
If you want add widgets, you need to use bokeh server. Because with bokeh, when you run the code once, the connection between the code and the visualisation is interrupted, therefore, if you want your actions on the visualisation to affect the python code, you need to run bokeh server.
Bokeh server uses Tornado, so it has Tornado embedded, which is a web server library of python. This allows it to serve applications in the browser.
For bokeh plots, you can easily use jupyter notebooks. But for bokeh server, it is better to use the editor.

---
`dir(object)`, `dir(bokeh.models.tools)` to get all methods of object, for instance all methods of figure object

Everything that has to do with interactivity and dynamic actions, you need to use a native bokeh data structure called `ColumnDataSource`. Pandas has limitations here, and interactive features like tooltip won’t work with pandas dataframes as input.

---
**Sample data**
Lots of sample data available from the library, example: `from bokeh.sampledata.iris import flowers`

---
**References**
1. [Udemy course - Interactive Data Visualization with Python and Bokeh](https://www.udemy.com/share/1020CE3@N4LR9O3EUV5aQg2i_2LGfIP17w8ZjgNdG30xYzYjoEetY2KY53dh5NRj4VJ3DAh56g==/)