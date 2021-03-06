# SPARQL connector for Spark

A library for querying SPARQL endpoints with Apache Spark, for Spark SQL and DataFrames.

SPARQL queries types SELECT, CONSTRUCT, ASK and DESCRIBE are supported.

## Requirements

This library requires Spark 1.5+

## Building
This library is build with sbt.
Use `sbt assembly` or `sbt +assembly` for cross compilation.

## Using with Spark shell
This package can be added to  Spark using the `--jars` command line option.  For example, to include it when starting the spark shell:

### Spark compiled with Scala 2.11
```
$SPARK_HOME/bin/spark-shell --jars spark-sparql-connector-spark1.5.2-scala2.11-1.0.0-SNAPSHOT.jar
```

### Spark compiled with Scala 2.10
```
$SPARK_HOME/bin/spark-shell --jars spark-sparql-connector-spark1.5.2-scala2.10-1.0.0-SNAPSHOT.jar
```

### Scala example
```
import de.usu.research.sake.sparksparql.SparqlContext

val service = "http://dbpedia.org/sparql"
val query = """SELECT ?property ?hasValue
WHERE {
  { <http://dbpedia.org/resource/Le_Figaro> ?property ?hasValue }
}"""

val dataFrame = sqlContext.sparqlQuery(service, query)
dataFrame.show()
```

## Using with PySpark shell
When using the PySpark shell, this package must be added both to the driver and the executors by using the `--driver-class-path` and `--jars` command line options.  For example, to include it when starting the pyspark shell:

### PySpark shell compiled with Scala 2.11
```
$SPARK_HOME/bin/pyspark --driver-class-path spark-sparql-connector-spark1.5.2-scala2.11-1.0.0-SNAPSHOT.jar --jars spark-sparql-connector-spark1.5.2-scala2.11-1.0.0-SNAPSHOT.jar
```

### PySpark shell compiled with Scala 2.10
```
$SPARK_HOME/bin/pyspark --driver-class-path spark-sparql-connector-spark1.5.2-scala2.10-1.0.0-SNAPSHOT.jar --jars spark-sparql-connector-spark1.5.2-scala2.10-1.0.0-SNAPSHOT.jar
```

### Python example
```
from pyspark.sql import SQLContext
sqlContext = SQLContext(sc)

query = """SELECT ?property ?hasValue
WHERE {
  { <http://dbpedia.org/resource/Le_Figaro> ?property ?hasValue }
}"""
df = sqlContext.read.format('de.usu.research.sake.sparksparql').options(service='http://dbpedia.org/sparql', query=query).load()
df.collect()
```

## Implementation details
- SPARQL queries are parsed using Apache Jena ARQ to extract the result variables. Then the queries are performed using Apache Jena JDBC driver
- Currently partitioning of the query results is not supported
- All result columns are mapped to StringType by default. To use a mapping to other types like IntegerType, BooleanType, TimestampType, etc take a look to the test suite `SparqlSuite`