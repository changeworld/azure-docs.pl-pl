---
title: Praca z usługi Azure Cosmos DB Cassandra API z platformy Spark
description: Ten artykuł jest strona główna Cosmos DB Cassandra API integracji z platformy Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 75d2930363b6ad1aeace22d7529df04f31deefe5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60893640"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Łączenie do usługi Azure Cosmos DB Cassandra API z platformy Spark

Ten artykuł stanowi jednej serii artykułów dotyczących integracji interfejsu API usługi Azure Cosmos DB Cassandra z platformy Spark. Artykuły obejmują łączności, operacje Language(DDL) definicji danych, podstawowe operacje Language(DML) manipulacji danych i zaawansowane integracji interfejsu API usługi Azure Cosmos DB Cassandra z platformy Spark. 

## <a name="prerequisites"></a>Wymagania wstępne
* [Aprowizuj konta interfejsu API usługi Azure Cosmos DB Cassandra.](create-cassandra-dotnet.md#create-a-database-account)

* Aprowizowanie preferowanego środowiska Spark [[usługi Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [usługi Azure HDInsight — Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | Inne].

## <a name="dependencies-for-connectivity"></a>Zależności dotyczące łączności
* **Łącznik platformy Spark dla bazy danych Cassandra:** Łącznik platformy Spark jest używany, połączyć się z interfejsem API usługi Azure Cosmos DB Cassandra.  Identyfikowanie i używana wersja łącznika na terenie [Maven central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) zgodny z platformami Spark i Scala wersje środowiska platformy Spark.

* **Biblioteka pomocnika usługi Azure Cosmos DB dla interfejsu API rozwiązania Cassandra:** Oprócz łącznik platformy Spark, potrzebujesz innej biblioteki o nazwie [azure-cosmos-cassandra-spark Pomocnika]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) z usługi Azure Cosmos DB. Ta biblioteka zawiera klasy zasad niestandardowych połączenia fabryki, a następnie spróbuj ponownie.

  Zasady ponawiania prób w usłudze Azure Cosmos DB jest skonfigurowany do obsługi HTTP status kodu 429 "("żądania dużej szybkości) wyjątki. Interfejs API usługi Azure Cosmos DB Cassandra tłumaczy tych wyjątków w błędy przeciążona natywnego protokołu bazy danych Cassandra i będzie można ponowić próbę z powrotem kompromisu. Ponieważ usługi Azure Cosmos DB używa modelu aprowizowanej przepływności, wyjątki ograniczanie szybkości żądania wystąpić, gdy ruchem przychodzącym/wychodzącym ocenia wzrost. Zasady ponawiania chroni względem wartości graniczne danych, które chwilowo przekroczenie przepływności przydzielonych do kolekcji zadań platformy spark.

  > [!NOTE] 
  > Zasady ponawiania może chronić zadań platformy spark względem tylko przechwytują skoki. Jeśli nie skonfigurowano wystarczającej liczby jednostek RU, wymagane do uruchomienia obciążenia, zasady ponawiania nie ma zastosowania i klasy zasad ponawiania prób ponownie zgłasza wyjątek.

* **Szczegóły połączenia konta usługi Azure Cosmos DB:** Usługi interfejsu API rozwiązania Cassandra usługi Azure nazwę konta, punkt końcowy konta i klucz.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Parametry konfiguracji przepływności łącznika Spark

W poniższej tabeli przedstawiono parametry konfiguracji przepływności specyficzne dla usługi Azure Cosmos DB Cassandra API dostarczonych przez łącznik. Aby uzyskać szczegółową listę wszystkich parametrów konfiguracji, zobacz [informacje o konfiguracji](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) strony repozytorium serwisu GitHub łącznika bazy danych Cassandra platformy Spark.

| **Nazwa właściwości** | **Wartość domyślna** | **Opis** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |Liczba wierszy w jednej partii. Ustaw ten parametr na wartość 1. Ten parametr jest używany w celu uzyskania większej przepływności dla dużych obciążeń. |
| spark.cassandra.connection.connections_per_executor_max  | Brak | Maksymalna liczba połączeń na węzeł przetwarzania. 10 * n jest odpowiednikiem połączenia o szybkości 10 każdy węzeł w klastrze Cassandra n węzłów. Tak Jeśli potrzebujesz 5 połączeń na węzeł przetwarzania dla 5 węzłami klastra oprogramowania Cassandra, następnie należy ustawić tę konfigurację do 25. Zmodyfikuj tę wartość na podstawie stopień równoległości lub liczbie funkcji wykonawczych, skonfigurowane dla zadań platformy spark.   |
| spark.cassandra.output.concurrent.writes  |  100 | Definiuje liczbę równoległych operacji zapisu, które mogą wystąpić na wykonawcy. Ponieważ "batch.size.rows" jest ustawiona na 1, pamiętaj odpowiednio skalować tę wartość. Zmodyfikować tę wartość, w oparciu o stopień równoległości lub przepływność, którą chcesz osiągnąć obciążenia. |
| spark.cassandra.concurrent.reads |  512 | Definiuje liczbę równoległych odczytów, które mogą wystąpić na wykonawcy. Zmodyfikować tę wartość, w oparciu o stopień równoległości lub przepływność, którą chcesz osiągnąć obciążenia  |
| spark.cassandra.output.throughput_mb_per_sec  | Brak | Definiuje przepływności łączna liczba operacji zapisu dla funkcji wykonawczej. Ten parametr może służyć jako górna ograniczenia dla przepływność zadanie platformy spark i bazowanie na aprowizowana przepływność kolekcji Cosmos DB.   |
| spark.cassandra.input.reads_per_sec| Brak   | Definiuje całkowita przepływność odczytu na wykonawcy. Ten parametr może służyć jako górna ograniczenia dla przepływność zadanie platformy spark i bazowanie na aprowizowana przepływność kolekcji Cosmos DB.  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | Określa liczbę wsadów poszczególnych zadań jednej platformy spark, które mogą być przechowywane w pamięci przed wysłaniem do interfejsu API rozwiązania Cassandra |
| spark.cassandra.connection.keep_alive_ms | 60000 | Definiuje okres, do której są dostępne nieużywane połączeń. | 

Dostosuj przepływności i stopień równoległości tych parametrów, na podstawie obciążeń, których oczekujesz od dla zadań platformy spark i przepływności, aprowizowanej dla konta usługi Cosmos DB.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Nawiązywanie połączenia z usługi Azure Cosmos DB Cassandra API z platformy Spark

### <a name="cqlsh"></a>cqlsh
Następujące polecenia szczegółowo opisują jak nawiązywanie połączenia z interfejsem API usługi Azure cosmos DB Cassandra z cqlsh.  Jest to przydatne do sprawdzania poprawności podczas wykonywania za pomocą przykładów platformie Spark.<br>
**Z systemem Linux lub Unix/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1.  Azure Databricks
Poniższy artykuł obejmuje usługi Azure Databricks w klastrze inicjowania obsługi konfiguracji klastra do łączenia się z interfejsem API usługi Azure Cosmos DB Cassandra i kilka notesów próbki, które obejmują operacje języka DDL, operacje DML i inne.<BR>
[Praca z interfejsem API usługi Azure Cosmos DB Cassandra z usługi Azure databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2.  Usługa Azure HDInsight Spark
Poniższy artykuł obejmuje usługę HDinsight Spark, inicjowanie obsługi administracyjnej, konfiguracji klastra do łączenia się z interfejsem API usługi Azure Cosmos DB Cassandra i kilka notesów próbki, które obejmują operacje języka DDL, operacje DML i nie tylko.<BR>
[Praca z usługi Azure Cosmos DB Cassandra API z usługi Azure HDInsight — Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3.  Ogólnie rzecz biorąc Spark środowiska
Gdy specyficzne dla usług PaaS oparta na platformie Azure Spark były powyżej, w tej sekcji opisano wszystkie ogólne środowisko Spark.  Łącznik zależności, importuje i konfiguracji sesji platformy Spark są szczegółowo opisane poniżej. W sekcji "Następne kroki" obejmuje przykłady kodu dla operacji DDL, operacje DML i nie tylko.  

#### <a name="connector-dependencies"></a>Łącznik zależności:

1. Dodaj współrzędnych maven, aby uzyskać [Cassandra łącznika Spark](cassandra-spark-generic.md#dependencies-for-connectivity)
2. Dodaj współrzędne maven [Biblioteka pomocnicza usługi Azure Cosmos DB](cassandra-spark-generic.md#dependencies-for-connectivity) dla interfejsu API rozwiązania Cassandra

#### <a name="imports"></a>Importy:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Konfiguracja sesji platformy Spark:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>Kolejne kroki

Następujące artykuły pokazują integracji platformy Spark z interfejsem API usługi Azure Cosmos DB Cassandra. 
 
* [Operacje języka DDL](cassandra-spark-ddl-ops.md)
* [Utwórz/insert operacji](cassandra-spark-create-ops.md)
* [operacje odczytu](cassandra-spark-read-ops.md)
* [Operacji UPSERT](cassandra-spark-upsert-ops.md)
* [Operacje usuwania](cassandra-spark-delete-ops.md)
* [Operacje agregacji](cassandra-spark-aggregation-ops.md)
* [Operacje kopiowania tabeli](cassandra-spark-table-copy-ops.md)
