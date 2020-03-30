---
title: Migrowanie danych relacyjnych od jednego do niewielu do interfejsu API SQL usługi Azure Cosmos DB
description: Dowiedz się, jak obsługiwać złożoną migrację danych dla relacji jeden-do-niewielu w interfejsie API SQL
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 467e9627a2623779bd808ca5aebdf76d8a5eda42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896638"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Migrowanie danych relacyjnych od jednego do niewielu do konta interfejsu API usługi Azure Cosmos DB SQL

Aby przeprowadzić migrację z relacyjnej bazy danych do interfejsu API SQL usługi Azure Cosmos DB, może być konieczne wprowadzenie zmian w modelu danych w celu optymalizacji.

Jedną z typowych transformacji jest denormalizacja danych przez osadzenie powiązanych podpozytów w jednym dokumencie JSON. Tutaj przyjrzymy się kilku opcji dla tego przy użyciu usługi Azure Data Factory lub Azure Databricks. Aby uzyskać ogólne wskazówki dotyczące modelowania danych dla usługi Cosmos DB, zapoznaj się [z modelowania danych w usłudze Azure Cosmos DB.](modeling-data.md)  

## <a name="example-scenario"></a>Przykładowy scenariusz

Załóżmy, że mamy następujące dwie tabele w naszej bazie danych SQL, Zamówienia i OrderDetails.


![Szczegóły zamówienia](./media/migrate-relational-to-cosmos-sql-api/orders.png)

Chcemy połączyć tę relację jeden do niewielu w jeden dokument JSON podczas migracji. Aby to zrobić, możemy utworzyć kwerendę T-SQL przy użyciu "FOR JSON", jak poniżej:

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

Wyniki tej kwerendy będą wyglądać poniżej: 

![Szczegóły zamówienia](./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png#lightbox)


W idealnym przypadku chcesz użyć pojedynczego działania kopiowania usługi Azure Data Factory (ADF), aby zbadać dane SQL jako źródło i zapisać dane wyjściowe bezpośrednio do ujścia usługi Azure Cosmos DB jako odpowiednie obiekty JSON. Obecnie nie jest możliwe wykonanie potrzebnej transformacji JSON w jednym działaniu kopiowania. Jeśli spróbujemy skopiować wyniki powyższej kwerendy do kontenera interfejsu API SQL usługi Azure Cosmos DB, zobaczymy OrderDetails pole jako właściwość ciągu naszego dokumentu, zamiast oczekiwanej tablicy JSON.

Możemy obejść to bieżące ograniczenie w jeden z następujących sposobów:

* **Użyj usługi Azure Data Factory z dwoma działaniami kopiowania:** 
  1. Pobierz dane w formacie JSON z SQL do pliku tekstowego w pośredniczącej lokalizacji magazynu obiektów blob oraz 
  2. Ładowanie danych z pliku tekstowego JSON do kontenera w usłudze Azure Cosmos DB.

* **Użyj usługi Azure Databricks do odczytu z języka SQL i zapisu w usłudze Azure Cosmos DB** — przedstawimy dwie opcje tutaj.


Przyjrzyjmy się tym podejściom bardziej szczegółowo:

## <a name="azure-data-factory"></a>Azure Data Factory

Mimo że nie możemy osadzić OrderDetails jako tablicy JSON w docelowym dokumencie usługi Cosmos DB, możemy obejść ten problem przy użyciu dwóch oddzielnych działań kopiowania.

### <a name="copy-activity-1-sqljsontoblobtext"></a>#1 działania kopiowania: SqlJsonToBlobText

W przypadku danych źródłowych używamy kwerendy SQL, aby uzyskać zestaw wyników jako pojedynczą kolumnę z jednym obiektem JSON (reprezentującym kolejność) na wiersz przy użyciu funkcji SQL Server OPENJSON i FOR JSON PATH:

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

![Kopia ADF](./media/migrate-relational-to-cosmos-sql-api/adf1.png)


W przypadku działania kopiowania sqljsonToBlobText wybieramy "Tekst rozdzielany" i wskazujemy go do określonego folderu w usłudze Azure@concatBlob Storage o dynamicznie wygenerowanej unikatowej nazwie pliku (na przykład ' (pipeline(). RunId,'.json').
Ponieważ nasz plik tekstowy nie jest tak naprawdę "rozdzielony" i nie chcemy, aby był analizowany w oddzielnych kolumnach za pomocą przecinków i chcemy zachować podwójne cudzysłowy ("), ustawiamy "Ogranicznik kolumny" na kartę ("\t") - lub inny znak nie występujący w danych - i "Znak cytatu" "Brak znaku cudzysłowu".

![Kopia ADF](./media/migrate-relational-to-cosmos-sql-api/adf2.png)

### <a name="copy-activity-2-blobjsontocosmos"></a>#2 działania kopiowania: BlobJsonToCosmos

Następnie zmodyfikujemy nasz potok usługi ADF, dodając drugie działanie kopiowania, które wygląda w usłudze Azure Blob Storage dla pliku tekstowego, który został utworzony przez pierwsze działanie. Przetwarza go jako źródło "JSON", aby wstawić do ujścia usługi Cosmos DB jako jeden dokument na JSON-wiersz znaleziony w pliku tekstowym.

![Kopia ADF](./media/migrate-relational-to-cosmos-sql-api/adf3.png)

Opcjonalnie dodajemy również działanie "Usuń" do potoku, aby usunąć wszystkie poprzednie pliki pozostałe w folderze /Orders/ przed każdym uruchomieniem. Nasz potok ADF wygląda teraz mniej więcej tak:

![Kopia ADF](./media/migrate-relational-to-cosmos-sql-api/adf4.png)

Po wyzwoleniu potoku powyżej widzimy plik utworzony w naszej pośredniczącej lokalizacji usługi Azure Blob Storage zawierającej jeden obiekt JSON na wiersz:

![Kopia ADF](./media/migrate-relational-to-cosmos-sql-api/adf5.png)

Widzimy również dokumenty zamówień z prawidłowo osadzonymi szczegółami zamówienia wstawionymi do naszej kolekcji Cosmos DB:

![Kopia ADF](./media/migrate-relational-to-cosmos-sql-api/adf6.png)


## <a name="azure-databricks"></a>Azure Databricks

Możemy również użyć platformy Spark in [Azure Databricks](https://azure.microsoft.com/services/databricks/) do kopiowania danych z naszego źródła bazy danych SQL do miejsca docelowego usługi Azure Cosmos DB bez tworzenia pośredniczących plików tekstowych/JSON w usłudze Azure Blob Storage. 

> [!NOTE]
> Aby uzyskać przejrzystość i prostotę, poniższe fragmenty kodu zawierają fikcyjne hasła bazy danych jawnie wbudowane, ale zawsze należy używać wpisów tajnych usługi Azure Databricks.
>

Najpierw tworzymy i dołączamy wymagany [łącznik SQL](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) i [biblioteki łączników usługi Azure Cosmos DB](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) do naszego klastra usługi Azure Databricks. Uruchom ponownie klaster, aby upewnić się, że biblioteki są ładowane.

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks1.png)

Następnie przedstawiamy dwie próbki, dla Scala i Python. 

### <a name="scala"></a>Scala
W tym miejscu możemy uzyskać wyniki kwerendy SQL z "FOR JSON" dane wyjściowe do DataFrame:

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks2.png)

Następnie łączymy się z naszą bazą danych i kolekcją Usługi Cosmos DB:

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

Na koniec definiujemy nasz schemat i from_json do zastosowania DataFrame przed zapisaniem go w kolekcji Usługi CosmosDB.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks3.png)


### <a name="python"></a>Python

Jako alternatywne podejście może być konieczne wykonanie przekształceń JSON w programie Spark (jeśli źródłowa baza danych nie obsługuje "FOR JSON" lub podobnej operacji) lub może być używana operacja równoległa dla bardzo dużego zestawu danych. Tutaj prezentujemy próbkę PySpark. Zacznij od skonfigurowania połączeń źródłowej i docelowej bazy danych w pierwszej komórce:

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

Następnie będziemy wysyłać zapytania do źródłowej bazy danych (w tym przypadku programu SQL Server) dla rekordów szczegółów zamówienia i zamówienia, umieszczając wyniki w platformie Spark Dataframes. Utworzymy również listę zawierającą wszystkie identyfikatory zamówień i pulę wątków dla operacji równoległych:

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

Następnie utwórz funkcję pisania zamówień do docelowej kolekcji interfejsu API SQL. Ta funkcja będzie filtrować wszystkie szczegóły zamówienia dla danego identyfikatora zamówienia, przekonwertować je na tablicę JSON i wstawić tablicę do dokumentu JSON, który zapiszemy w docelowej kolekcji interfejsu API SQL dla tej kolejności:

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

Na koniec wywołamy powyższe za pomocą funkcji mapy w puli wątków, aby wykonać równolegle, przechodząc na liście identyfikatorów zamówień, które utworzyliśmy wcześniej:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
W obu podejściach, na końcu, powinniśmy uzyskać poprawnie zapisane osadzone OrderDetails w ramach każdego dokumentu zamówienia w kolekcji Usługi Cosmos DB:

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks4.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [modelowaniu danych w usłudze Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/modeling-data)
* Dowiedz [się, jak modelować i dzielić dane w usłudze Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)
