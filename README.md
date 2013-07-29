leaflet-docset
==============

Documentation for the [Leaflet](http://leafletjs.com/) JavaScript map library in the Dash [docset](http://kapeli.com/docsets) format, ready for offline reading and searching.

To Use Docs
-----------
1.  Install a docset viewer, like [Dash](http://kapeli.com/dash).
2.  In Dash's preferences, add the following docset feed URL: [https://raw.github.com/drewda/leaflet-docset/master/Leaflet.xml](dash-feed://https%3A%2F%2Fraw.github.com%2Fdrewda%2Fleaflet-docset%2Fmaster%2FLeaflet.xml)

![image](add-docset-feed-url-screenshot.png)

To Generate Docs
----------------
The docs are copied from the Leaflet site and indexed using a Ruby script. To set up and run the Ruby script:

````   
   cd leaflet-docset
   bundle install
   rake
   
````

Future Improvements
-------------------
[ ] The [classification of sections](https://github.com/drewda/leaflet-docset/blob/master/Rakefile#L80-110) (into class, method, interface, etc.) is fast and loose. It could use improvement.
