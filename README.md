# Graph explorer

A highly interactive dashboard to satisfy varying ad-hoc information needs across a multitude of metrics in a very powerful way:  

* The core of graph-explorer is a database containing your metrics extended with tags
(key-value pairs that represent server, service, type, unit, ...)
* You can use expressive queries which leverage this metadata to filter targets, group them into graphs, process and aggregate them on the fly.
Something like SQL but metrics for rows and a list of graph definitions as a result set.  All graphs are built dynamically.

The graphs themselves support annotated events and are also interactive because it uses [timeserieswidget](https://github.com/vimeo/timeserieswidget)
Furthermore, we aim for minimal, hackable code and as simple as possible deploy/install.

![Screenshot](https://raw.github.com/vimeo/graph-explorer/master/screenshots/screenshot.png)

It also has:

* [dashboards](https://github.com/vimeo/graph-explorer/wiki/Dashboards) which are pages that show N queries along with their results (0-N graphs each) and a (url-driven) field that gets applied to all queries, which you can use to narrow down to a specific server, apply a timeframe, etc.
* an [alerting system](https://github.com/vimeo/graph-explorer/wiki/Alerting) allowing you to set tresholds on queries or plain old graphite query strings.

## Learn the basics

* [Tutorial](https://github.com/vimeo/graph-explorer/wiki/Tutorial)
* [The query interface explained](https://github.com/vimeo/graph-explorer/wiki/the-Query-interface)
* introduction to metrics 2.0 & Graph-Explorer: [https://vimeo.com/87194301](https://vimeo.com/87194301)

## Metrics 2.0

In graphite, a metric has a name and a corresponding time series of values.
Graph-explorer's metrics are structured: they contain key-value tags that describe all their attributes, the unit, the metric type, etc.
You can generate the tag database by using plugins that parse metrics using regular expressions, or by tagging them as they flow into graphite.
See the [Structured Metrics page](https://github.com/vimeo/graph-explorer/wiki/Structured-Metrics)


## GEQL, the Graph-Explorer Query Language

the Graph-Explorer Query Language is designed to:

* be minimal, use a simple syntax and get a lot done with little input.
* let you compose graphs from metrics in a flexible way:
  you can use tags and pattern matching to filter, group, process and aggregate targets and manipulate how the graph gets displayed.
* let you create custom views of the exact information you need, and let you compare and correlate across different aspects.

At the most basic level you start by typing patterns that will filter out the metrics you're looking for.
Then, you can extend the query by typing statements that have special meanings.

For more information see the [Graph-Explorer Query Language](https://github.com/vimeo/graph-explorer/wiki/GEQL) page


## Dependencies

* python2: python2.6 or higher
* install elasticsearch and run it (super easy, see [docs](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/setup.html) just set a unique cluster name)
* Graphite 0.9.10 or higher (tested with 0.9.12)
* python2-pysqlite (if you want to use the alerting feature)

## Installation

Get a code checkout and initialize all git submodules, like so:

```
git clone --recursive https://github.com/vimeo/graph-explorer.git
```
This will give you the latest bleeding edge code (master branch), which may be buggy.
You can switch to the latest stable release with `git checkout v<version>`

You probably want to set up a [virtualenv](http://www.virtualenv.org/en/latest/) before installing.

To install, along with required packages:

```
python setup.py install
```

Or, if you're planning on developing Graph Explorer, install in-place with setuptools "Development Mode":

```
python setup.py develop
```

The [releases page](https://github.com/vimeo/graph-explorer/releases) has more info, but don't download from there, the downloads don't contain
the needed submodules!
Graph Explorer version numbering is based on [semver](http://semver.org/).

## Configuration of graph-explorer

* edit config.py for basic config options.  If you want annotated events using [anthracite](https://github.com/Dieterbe/anthracite) set `anthracite_url`
* have a look at preferences.py, this is where you can configure timezone, targets colors, a few graph options, etc.
* [populate an elasticsearch database with structured metrics](https://github.com/vimeo/graph-explorer/wiki/Structured-Metrics)

## Configuration of graphite server

you'll need a small tweak to allow this app to request data from graphite.
For apache2 this works:

    Header set Access-Control-Allow-Origin "*"
    Header set Access-Control-Allow-Methods "GET, OPTIONS, POST"
    Header set Access-Control-Allow-Headers "origin, authorization, accept"

## Running

### Linux / Unix

* default, with Paste (included):

`run_graph_explorer.py` and your page is available at `<ip>:8080`

* alternatively, if you use gunicorn, you can run it with multi-workers like so:
`gunicorn -w 4 app:'default_app()' -b 0.0.0.0:8080`

### Windows

`python %VIRTUAL_ENV%\scripts\run_graph_explorer.py` and your page is available at `<ip>:8080`

or with Powershell:

`python $env:VIRTUAL_ENV/scripts/run_graph_explorer.py`


## Troubleshooting
* no graphs show up and I don't know why.

first check in the top section if there are target matching and 'total graphs' is > 0.  
if not, your query expression might be too restricting,
or maybe it didn't find your metrics from metrics.json (see 'targets matching: x/total')  
if yes, check for any errors in the javascript console, (in firefox you need firebug, in chrome and such 'tools->javascript console')

also check all network requests in the network tab, and make sure they return http 200 or 304
especially, check that the http requests to `graphite/render/?<...>` return actual data.
you may be suffering from [this graphite bug](https://github.com/graphite-project/graphite-web/issues/289)
or [this graphite bug](https://github.com/graphite-project/graphite-web/issues/576) or maybe your graphite version is too old.

* I get some error wrt graphite/apache cors access restriction

see section 'Configuration of graphite server' above

* Graph Explorer pulls too much data over the network

This is because graphite 
<a href="https://github.com/graphite-project/graphite-web/issues/153">doesn't support consolidation on its data API</a> yet.

## Documentation

[wiki page](https://github.com/vimeo/graph-explorer/wiki)


## Unit tests

```
python setup.py test
```

## Getting in touch

* irc: #graph-explorer on freenode
* github issues for bugs, feature requests, questions, feedback
