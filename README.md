# geo_pyspark

GeoSpark python bindings.

example:

```python
from pyspark.sql import SparkSession
from geo_pyspark.register import GeoSparkRegistrator


spark = SparkSession.builder.\
        getOrCreate()

GeoSparkRegistrator.registerAll(spark)

df = spark.sql("""SELECT st_geomfromwkt('POINT(6.0 52.0)') as geom""")

df.show()

```
    +------------+
    |        geom|
    +------------+
    |POINT (6 52)|
    +------------+

```python
print(df.collect())

>> [Row(geom=Point(x=6.0, y=52.0))]

print(df.toPandas())
>>         geom
        0  Point(x=6.0, y=52.0)
```

## convert to pandas and geopandas

```python
counties = spark.\
    read.\
    option("delimiter", "|").\
    option("header", "true").\
    csv("data/counties.csv")

counties.createOrReplaceTempView("county")

df = spark.sql(
        f"SELECT *, st_geomfromwkt(geom) as geometry from county"
)

pd_df = df.toPandas()
gdf = gpd.GeoDataFrame(pd_df, geometry="geometry")

gdf.plot()
plt.show()
```

<img src="https://github.com/Imbruced/geo_pyspark/blob/master/geo_pyspark/data/geopandas_plot.PNG" width="250">

