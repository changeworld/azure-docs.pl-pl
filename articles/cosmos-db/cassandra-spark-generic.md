---
title: Praca z interfejsem API Cassandra usługi Azure Cosmos DB firmy Spark
description: Ten artykuł jest stroną główną integracji interfejsu API usługi Cosmos DB Cassandra z platformy Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: cb34ea44c069f067d13a6480531a94a1a515f380
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70241240"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Łączenie z interfejsem API Cassandra usługi Azure Cosmos DB za pomocą platformy Spark

Ten artykuł jest jednym z serii artykułów na temat integracji interfejsu API usługi Azure Cosmos DB Cassandra z platformy Spark. Artykuły obejmują łączność, operacje języka DDL(DDL) definicji danych, podstawowe operacje języka DML (Data Manipulation Language) i zaawansowaną integrację interfejsu API usługi Azure Cosmos DB Cassandra z platformy Spark. 

## <a name="prerequisites"></a>Wymagania wstępne
* [Aprowizuj konto interfejsu API usługi Azure Cosmos DB Cassandra.](create-cassandra-dotnet.md#create-a-database-account)

* Aprowizuj wybrane środowisko platformy Spark [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [Azure HDInsight-Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | Inne].

## <a name="dependencies-for-connectivity"></a>Zależności dla łączności
* **Złącze Iskry dla Cassandra:** Łącznik platformy Spark jest używany do łączenia się z interfejsem API Cassandra usługi Azure Cosmos DB.  Identyfikowanie i używanie wersji łącznika znajdującej się w [centrum Maven,]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) która jest zgodna z wersjami Spark i Scala środowiska Spark.

* **Biblioteka pomocnicza usługi Azure Cosmos DB dla interfejsu API Cassandra:** Oprócz łącznika platformy Spark potrzebna jest inna biblioteka o nazwie [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) z usługi Azure Cosmos DB. Ta biblioteka zawiera niestandardowe ustawienia fabryczne połączenia i ponawiania prób klas zasad.

  Zasady ponawiania próby w usłudze Azure Cosmos DB są skonfigurowane do obsługi wyjątków kodu stanu HTTP 429("Request Rate Large"). Interfejs API Cassandra usługi Azure Cosmos DB tłumaczy te wyjątki na przeciążone błędy w protokole macierzystym Cassandra i można ponowić próbę z wycofywaniem. Ponieważ usługa Azure Cosmos DB używa aprowizowanego modelu przepływności, wyjątki ograniczające szybkość żądania występują, gdy zwiększa się szybkość transferu danych przychodzących/wychodzących. Zasady ponawiania próby chroni zadania iskrzenie przed skoki danych, które na chwilę przekraczają przepływności przydzielone dla kontenera.

  > [!NOTE] 
  > Zasady ponawiania próby mogą chronić zadania iskrzenie przed chwilowymi skokami. Jeśli nie skonfigurowano wystarczającej liczby procesorów RU wymaganych do uruchomienia obciążenia, zasady ponawiania nie ma zastosowania, a klasa zasad ponawiania próby ponownie wykręć wyjątek.

* **Szczegóły połączenia konta usługi Azure Cosmos DB:** Nazwa konta interfejsu API platformy Azure Cassandra, punkt końcowy konta i klucz.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Parametry konfiguracji przepływności łącznika platformy Spark

W poniższej tabeli wymieniono parametry konfiguracji konfiguracji specyficzne dla interfejsu API usługi Azure Cosmos DB Cassandra dostarczone przez łącznik. Aby uzyskać szczegółową listę wszystkich parametrów konfiguracji, zobacz stronę [odwołania do konfiguracji](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) repozytorium GitHub łącznika Spark Cassandra.

| **Nazwa właściwości** | **Wartość domyślna** | **Opis** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |Liczba wierszy na pojedynczą partię. Ustaw ten parametr na 1. Ten parametr jest używany do osiągnięcia wyższej przepływności dla dużych obciążeń. |
| spark.cassandra.connection.connections_per_executor_max  | Brak | Maksymalna liczba połączeń na węzeł na wykonawcę. 10*n odpowiada 10 połączeń na węzeł w klastrze cassandra n-węzła. Tak więc, jeśli potrzebujesz 5 połączeń na węzeł na wykonawca dla klastra Cassandra 5 węzłów, należy ustawić tę konfigurację na 25. Zmodyfikuj tę wartość na podstawie stopnia równoległości lub liczby executors, dla których są skonfigurowane zadania spark.   |
| spark.cassandra.output.concurrent.writes  |  100 | Definiuje liczbę zapisów równoległych, które mogą wystąpić na wykonawcę. Ponieważ ustawisz "batch.size.rows" na 1, upewnij się, że odpowiednio skalujesz tę wartość. Zmodyfikuj tę wartość na podstawie stopnia równoległości lub przepływności, które chcesz osiągnąć dla obciążenia. |
| spark.cassandra.concurrent.reads |  512 | Definiuje liczbę odczytów równoległych, które mogą wystąpić na wykonawcę. Zmodyfikuj tę wartość na podstawie stopnia równoległości lub przepływności, którą chcesz osiągnąć dla obciążenia  |
| spark.cassandra.output.throughput_mb_per_sec  | Brak | Definiuje całkowitą przepływność zapisu na wykonawcę. Ten parametr może służyć jako górny limit przepływności zadania iskrzenie i oprzeć go na aprowizowanej przepływności kontenera usługi Cosmos.   |
| spark.cassandra.input.reads_per_sec| Brak   | Definiuje całkowitą przepływność odczytu na wykonawcę. Ten parametr może służyć jako górny limit przepływności zadania iskrzenie i oprzeć go na aprowizowanej przepływności kontenera usługi Cosmos.  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | Określa liczbę partii na pojedyncze zadanie iskrowe, które mogą być przechowywane w pamięci przed wysłaniem do api Cassandra |
| spark.cassandra.connection.keep_alive_ms | 60000 | Określa okres czasu, do którego nieużywane połączenia są dostępne. | 

Dostosuj przepływność i stopień równoległości tych parametrów na podstawie obciążenia oczekiwanego dla zadań platformy spark i przepływności, która została aprowizowana dla konta usługi Cosmos DB.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Łączenie się z interfejsem API Cassandra usługi Azure Cosmos DB z platformy Spark

### <a name="cqlsh"></a>cqlsh ( cqlsh )
Poniższe polecenia szczegółowo jak połączyć się z interfejsem API Cassandra usługi Azure CosmosDB z cqlsh.  Jest to przydatne do sprawdzania poprawności podczas uruchamiania za pośrednictwem przykładów w spark.<br>
**Z Linuksa/Uniksa/Maca:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1. Usługa Azure Databricks
W tym artykule opisano aprowizacji klastra usługi Azure Databricks, konfiguracji klastra do łączenia się z interfejsem API Cassandra usługi Azure Cosmos DB i kilku przykładowych notesów, które obejmują operacje DDL, operacje DML i inne.<BR>
[Praca z interfejsem API Cassandra usługi Azure Cosmos DB z witryny Databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2. Platforma Azure HDInsight-Spark
W tym artykule opisano usługę HDinsight-Spark, inicjowanie obsługi administracyjnej, konfigurację klastra do łączenia się z interfejsem API Cassandra usługi Azure Cosmos DB i kilka przykładowych notesów obejmujących operacje DDL, operacje DML i inne.<BR>
[Praca z interfejsem API Cassandra usługi Azure Cosmos DB z usługi Azure HDInsight-Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3. Środowisko iskrowe w ogóle
Podczas gdy powyższe sekcje były specyficzne dla usług PaaS opartych na platformie Azure Spark, ta sekcja obejmuje wszystkie ogólne środowisko platformy Spark.  Zależności łącznika, importy i konfiguracja sesji platformy Spark są opisane poniżej. Sekcja "Następne kroki" obejmuje przykłady kodu dla operacji DDL, operacji DML i innych.  

#### <a name="connector-dependencies"></a>Zależności łącznika:

1. Dodaj współrzędne maven, aby uzyskać [łącznik Cassandra dla platformy Spark](cassandra-spark-generic.md#dependencies-for-connectivity)
2. Dodawanie współrzędnych maven dla [biblioteki pomocniczej usługi Azure Cosmos DB](cassandra-spark-generic.md#dependencies-for-connectivity) dla interfejsu API Cassandra

#### <a name="imports"></a>Przywozu:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Konfiguracja sesji iskierki:

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

## <a name="next-steps"></a>Następne kroki

W poniższych artykułach pokazano integrację platformy Spark z interfejsem API Cassandra usługi Azure Cosmos DB. 
 
* [Operacje DDL](cassandra-spark-ddl-ops.md)
* [Tworzenie/wstawianie operacji](cassandra-spark-create-ops.md)
* [Operacje odczytu](cassandra-spark-read-ops.md)
* [Operacje upsert](cassandra-spark-upsert-ops.md)
* [Usuwanie operacji](cassandra-spark-delete-ops.md)
* [Operacje agregacji](cassandra-spark-aggregation-ops.md)
* [Operacje kopiowania tabeli](cassandra-spark-table-copy-ops.md)
