# PySiddhi

***PySiddhi*** is a Python wrapper for [Siddhi](https://siddhi-io.github.io/siddhi/). Which can listens to events from data streams, detects complex conditions
described via a ***Streaming SQL language***, and triggers actions. It performs both ***Stream Processing*** and 
***Complex Event Processing*** on streaming data. Its Siddhi core is written in Java library. 

- PySiddhi wraps [Siddhi 5](https://siddhi-io.github.io/siddhi/)

## Content

* Introduction and Quick Demo (this page)
* [Installation Guide](Installation-Guide.md)
* [Run PySiddhi](Run-PySiddhi.md)
* [Debug PySiddhi](Debugging-Siddhi-Queries.md)
* [Advanced Concepts of PySiddhi](Using-Siddhi-from-Python.md)
    
## Features

- [x] Basic functionality of Siddhi 5.x.x
- [x] Siddhi Debugger
- [x] Support to Siddhi Extensions Loading

## Installation

PySiddhi can be installed using pip.

```
pip install pysiddhi
```

For detail insulation and prerequisite refer section on [Installation Guide](Installation-Guide). 

## Quick Demo
Following is a quick demo of how to use PySiddhi. For comprehensive demo please refer [Quick-Demo-PySiddhi](Run-PySiddhi.md)

**Step 1** - Define filter using Siddhi Query.

```python
siddhiManager = SiddhiManager()
# Siddhi Query to filter events with volume less than 150 as output
siddhiApp = "define stream cseEventStream (symbol string, price float, volume long);" + \
            "@info(name = 'query1') " + \
            "from cseEventStream[volume < 150] " + \
            "select symbol, price " + \
            "insert into outputStream;"

# Generate runtime
siddhiAppRuntime = siddhiManager.createSiddhiAppRuntime(siddhiApp)
```
For more details on Siddhi Query Language, refer [Siddhi Query Language Guide](https://siddhi-io.github.io/siddhi/).

**Step 2** - Define a listener for filtered events.

```python
# Add listener to capture output events
class QueryCallbackImpl(QueryCallback):
    def receive(self, timestamp, inEvents, outEvents):
        PrintEvent(timestamp, inEvents, outEvents)
siddhiAppRuntime.addCallback("query1",QueryCallbackImpl())
```
**Step 3** - Test filter query using sample input events.

```python
# Retrieving input handler to push events into Siddhi
inputHandler = siddhiAppRuntime.getInputHandler("cseEventStream")

# Starting event processing
siddhiAppRuntime.start()

# Sending events to Siddhi
inputHandler.send(["IBM",700.0,LongType(100)])
inputHandler.send(["WSO2", 60.5, LongType(200)])
inputHandler.send(["GOOG", 50, LongType(30)])
inputHandler.send(["IBM", 76.6, LongType(400)])
inputHandler.send(["WSO2", 45.6, LongType(50)])

# Wait for response
sleep(0.1)
```
**Output**

The 3 events with volume less than 150 are printed in log.

```log
INFO  EventPrinter - Events{ @timestamp = 1497708406678, inEvents = [Event{timestamp=1497708406678, id=-1, data=[IBM, 700.0], isExpired=false}], RemoveEvents = null }
INFO  EventPrinter - Events{ @timestamp = 1497708406685, inEvents = [Event{timestamp=1497708406685, id=-1, data=[GOOG, 50], isExpired=false}], RemoveEvents = null }
INFO  EventPrinter - Events{ @timestamp = 1497708406687, inEvents = [Event{timestamp=1497708406687, id=-1, data=[WSO2, 45.6], isExpired=false}], RemoveEvents = null }
```

**Clean Up** - Remember to shutdown the Siddhi Manager when your done.

```python
siddhiManager.shutdown()
```

## Contribution 

_PySiddhi is initiated by a project for Google Summer of Code 2017 Program._

Contributed by: __Madhawa Vidanapathirana__ </br>
Email: madhawavidanapathirana@gmail.com </br>
Organization: University of Moratuwa, Sri Lanka.

## Support and Contribution

* We encourage users to ask questions and get support via <a target="_blank" href="https://stackoverflow.com/questions/tagged/siddhi">StackOverflow</a>, make sure to add the `siddhi` tag to the issue for better response.

* If you find any issues related to the extension please report them on <a target="_blank" href="https://github.com/siddhi-io/siddhi-execution-string/issues">the issue tracker</a>.

* For production support and other contribution related information refer <a target="_blank" href="https://siddhi.io/community/">Siddhi Community</a> documentation.

