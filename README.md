# Slashem (SolrRogue)

Slashem (SolrRogue) is a type-safe internal Scala DSL for constructing and
executing find requests against SOLR. It is a rogue-like for solr. It is not
currently full expressive to SOLR's query functionality, but covers the main
use cases that we encountered.

## Building and Installation

Use sbt (simple-build-tool) to build:

    $ sbt clean update package

The finished jar will be in 'target/'.

## Hooks to overload certain features

Slashem provides two main hooks for extension. Most people will probably
wish to overload the default logging mechanism which throws away everything.
To do this simply implement the SolrQueryLogger trait and set the logger param
on your Schema objects to your custom logger.

The other hook is only useful if you are using Solr for geospatail information,
we provide a trait called SolrGeoHash which has two required functions, namely
coverString and rectCoverString. Most people will not need to implement this.

### Multiple Solr cores & non-stanrd query paths

Support for multiple cores is done by overriding "core" in the model (wich is an Option[String]).
If you have a non-standard query path you can override queryPath in your model.

## Examples

[QueryTest.scala](https://github.com/foursquare/slashem/blob/master/src/test/scala/com/foursquare/slashem/QueryTest.scala) contains sample queries and shows the corresponding query.
[SolrRogueTest.scala](https://github.com/foursquare/slashem/blob/master/src/test/scala/com/foursquare/slashem/SolrRogueTest.scala) countains some sample records.

A basic query against the SUserTest might look something like

    val q = SUserTest where (_.fullname eqs "jon")

This would do a phrase search for "jon" against the fullname field in SUserTest.
A more complex query might specify a different query parser like so:

    val q = SUserTest where (_.fullname eqs "holden") useQueryType("edismax")

Frequently with edismax queries you want to run your query against multiple fields
and or boost queries which match the entire phrase. The followingis an example of how
to do this:

    val q = SVenueTest where (_.default contains lols) useQueryType("edismax") phraseBoost(_.text, 1) phraseBoost(_.name,200) phraseBoost(_.aliases,25) queryField(_.text) queryField(_.ngram_name, 0.2) queryField(_.tags, 0.01) tieBreaker(0.2)


## Dependencies

lift, joda-time, junit, finagle, jackson. These dependencies are managed by 
the build system. Note: some of the transitive dependencies may fail to resolve
from the central maven. If you are using sbt you can fix this by adding

   ivyXML := (
   <dependencies>
    <exclude module="jmxtools"/>
    <exclude module="jmxri"/>
   </dependencies>
  )

## Warnings still a very early version. There are likely bugs (sorry!). Let us know
if you find any. While we can't promise timely fixes, it will help :)

## Maintainers

Slashem (SolrRogue) was initial developed by Foursquare Labs for internal use. 
The majority of our solr related calls at Foursquare go through this library. 
The current maintainers are:

- Jon Shea jonshea@foursquare.com
- Govind Kabra govind@foursquare.com
- Adam Alix aalix@foursquare.com

Contributions welcome!