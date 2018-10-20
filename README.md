# Lengaburu Traffic
Solution to the problem given by GeekTrust (https://www.geektrust.in/api/pdf/open/PS3 )


# Setup of Orbits, Vehicles and other preferences
-------------------------------------------------
## Weather Data File (present as data/weather.txt)
------------------
Supply data in this manner - Weather Name,Crater Impact
For Crater Impact, give a negative if it causes a decrease in craters and positive if it causes an increase

Sample Record:
"sunny",-10

## Orbits Data File (present as data/orbits1.txt and data/orbits.txt)
-----------------
Supply orbits in this manner - Orbit Name,distance,craters,start point,end point
All orbit names should be unique, start point and end point should be a part of destinations.txt file
Each orbit record should appear in a newline
Sample Record:
"Orbit1",18,20,"Silk Dorb","Hallitharam"

## Vehicles Data File (present as data/vehicles.txt)
-----------------
Supply vehicle data in this manner - Vehicle,speed,time to cross a crater in mins,1 if travels in sunny weather and 0 otherwise,1 if travels in rainy weather and 0 otherwise,1 if travels in windy weather and 0 otherwise,vehicle preferred order.All vehicle names should be unique.Each vehicle record should appear in a 
newline.The order for flags in weather should be the same order as present in the Weather Data file desribed above.

Sample Record:
"Bike",10,2,1,1,0,1	


# Running the script 
---------------------
Requirement : 
Python 2

Package Requirements for Running Test cases:
mock
unittest

No dependencies for the running 
To execute the script , run the python script runLengaburu.py. Ensure that all files are present in the same folder.

 * git clone https://github.com/ashavish/gttraffic
 * cd gttraffic

For Problem 1: Example

python ./gttraffic/runLengaburuSolution.py 
 * Select problem. press 1 for Problem 1 and 2 for Problem 2: 1
 * Enter traffic limit for Orbit1 : 12
 * Enter traffic limit for Orbit2 : 10
 * Input weather.Options are sunny/rainy/windy : sunny


For Problem 2: Example

python ./gttraffic/runLengaburuSolution.py 
 * Select problem. press 1 for Problem 1 and 2 for Problem 2: 2
 * Enter traffic limit for Orbit1 : 5
 * Enter traffic limit for Orbit2 : 10
 * Enter traffic limit for Orbit3 : 20
 * Enter traffic limit for Orbit4 : 20
 * Input weather.Options are sunny/rainy/windy : windy


To run the script as a complete pip installable package
-------------------------------------------------------

Navigate to the root
cd gttraffic

Run -
```python
python pip install .
```

# TESTING
---------

Requirement : 
Python 2

Package Requirements for Running Test cases:
 * mock
 * unittest

Run :

* Navigate to root folder 
```python
cd gttraffic 
python ./tests/runLengaburuTestCases.py

```

OR

The tests can be run directly by calling  setup.py from root folder

```python
cd gttraffic 
python setup.py test

```

It runs a sample set of 25 test cases, which test some of the critical functionality within the application.
Its not exhaustive to the extent of testing all possible cases, but gives an undertanding of how the testing
can be enhanced further.

The tests broadly cover the following areas :

 * It tests the overall expected outputs for problem 1 and problem 2 (Incorporated in this, even though its not strictly a unit testing)
 * It tests the overall expected Route values for different start points and destinations. It also
tests the condition of extensibility by adding a new end point "Alagnamrok" between Silk Dorb and Hallitharam.
Alagnamrok is connected to Silk Dorb via Orbit5 and Hallitharam via Orbit6.
 * It tests the expected travel times for a given Orbit and Vehicle combinations under different weather and traffic conditions
 * It tests for vehicle restrictions given certain weather conditions.


# Design Notes 
---------------


The setup data is taken from text files. Its possible to extend functionality to accept data from any kind of User Interface. In case of any input data structure change, modification just needs to be made to one class minimizing the impact on all other classes.

InputDataStructures - This class is responsible for ensuring that the data structures are present in a form thats consumable for all the other classes.  For any change in input data structures like the input text files, this class only needs to change to accomodate the same.

Orbits data is setup in the text file, hence its possible to extend and add more orbits without any changes to the code.

=> Orbits - Orbit class stores orbit related information and implements orbit methods. The method model_orbit_environment models the orbit condition for a particular weather and traffic conditions.

=> OrbitGraph - Class for mapping orbit connections. For each point, it maintains a list of adjacent points and orbits. Sample structure of how it stores these :

{'Silk Dorb': {'Hallitharam': ['Orbit2', 'Orbit1']}, 'Hallitharam': {'Silk Dorb': ['Orbit2', 'Orbit1']}}

It implements the following methods :

create_maps - Creates the list of adjacent points for each point

get_connecting_orbits - Given a point, gets the orbits which connects to the point

get_connected_points - Given a point, gets the adjacent points

get_orbits_points_to_visit - A recursive method which computes the possible routes given a point and a set of points to visit. This is extendable to accomodate more orbits and more destinations.
Testing was done with a new destination Alagnamrok and by increasing the number of orbits

route_mapper - A wrapper method for get_orbits_points_to_visit and calls the recursive method. It outputs the routes in a specific format.


=> Vehicles - The Vehicle class is a super class. Its sub classes are the Bike, SuperCar and the TukTuk. They dont have any specific methods as of now, but the sub classes were created more to enable modeling future behavior changes for each type of vehicle.

Vehicle data - its possible to change certain behaviours of existing vehicles, but adding new vehicles, would entail adding a new sub class.

=> CityTraffic - This class acts as a wrapper and initializes all traffic components like Orbits, OrbitGraph and Vehicles.It has a separate method to model the environment of traffic and weather and calls the individual orbit and vehicle class's model_environment methods.


=> RouteSelection - 

This class implements route selection.The route is considered to comprise a set of orbits.

Given a point and a set of points to visit and for the prevailing traffic conditions, the route selection class outputs the best route & vehicle to consider.  

This class uses the OrbitGraph class's orbit map to get the routes. Then it creates the possible set of route vehicle combinations.If the requirement changes to enable a user to use any vehicle for any orbit instead of current restriction of one vehicle for all the orbits in the route, this method is the only place that would need to undergo the change. 

Given the route vehicle combinations, the time taken for each route vehicle combination is computed. Currently it gets the time for all routes but this can be optimized further. If the number of orbits increase, then this can optimized to stop computation the moment an upper bound is reached while computing the time for indiividual orbits in the route.

Currently the orbit time is computed by passing the vehicle object to the Orbit Class method "get_travel_time". This uses a dictionary object to keep track of travel times for orbit and vehicle combinations. If already computed, it just uses the stored value. If not, it computes it.



# BUILD 
-------
Python scripts can be directly run 

A setup.py has been provided to enable direct pip installation if the python version is 2.7.


The package can be installed using :

pip install git+git://github.com/ashavish/gttraffic.git

To use this, open python console and type the following code:

```python
import gttraffic
gttraffic.run_lengaburu()
``` 

If using the package, the data files comes in the package, and methods havent been provided to change the base route data as of now. But if required, this can be customized.
