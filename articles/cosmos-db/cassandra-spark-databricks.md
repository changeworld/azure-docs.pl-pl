---
title: Dostęp do usługi Azure Cosmos DB Cassandra API z usługi Azure Databricks
description: W tym artykule opisano, jak pracować z usługą Azure Cosmos DB bazy danych Cassandra API z usługi Azure Databricks.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 37a06b19285c1196b5d87830ea176d4bd0d4eade
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60894015"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Dostęp do interfejsu API usługi Azure Cosmos DB Cassandra danych z usługi Azure Databricks

Ten artykuł zawiera instrukcje workwith interfejsu API usługi Azure Cosmos DB Cassandra z platformy Spark na [usługi Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks).

## <a name="prerequisites"></a>Wymagania wstępne

* [Aprowizacja konta interfejsu API usługi Azure Cosmos DB Cassandra](create-cassandra-dotnet.md#create-a-database-account)

* [Przejrzyj podstawowe informacje dotyczące nawiązywania połączenia z interfejsem API usługi Azure Cosmos DB Cassandra](cassandra-spark-generic.md)

* [Zainicjuj obsługę klastra usługi Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Przejrzyj przykłady kodu do pracy z interfejsem API bazy danych Cassandra](cassandra-spark-generic.md#next-steps)

* [Używać cqlsh do sprawdzania poprawności, jeśli więc chcesz](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Konfiguracja wystąpienia interfejsu API rozwiązania Cassandra łącznika bazy danych Cassandra:**

  Łącznik dla interfejsu API rozwiązania Cassandra wymaga szczegóły połączenia bazy danych Cassandra do zainicjowania jako część kontekstu aparatu spark. Podczas uruchamiania notesu usługi Databricks kontekstu aparatu spark jest już zainicjowany i nie jest zalecane, aby zatrzymać i ponownie zainicjować go. Jedno rozwiązanie to można dodać konfiguracji wystąpienia interfejsu API rozwiązania Cassandra na poziomie do klastra, w konfiguracji klastra spark. Jest to jednorazowa działania dla klastra. Dodaj następujący kod do konfiguracji platformy Spark, jak spacjami parę klucz-wartość:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Dodawanie wymaganych zależności

* **Łącznik platformy Spark Cassandra:** — aby Integracja z interfejsem API usługi Azure Cosmos DB Cassandra z platformą Spark, Cassandra, łącznik, musi być podłączona do usługi Azure Databricks w klastrze. Aby dołączyć do klastra:

  * Przejrzyj wersja środowiska uruchomieniowego Databricks wersji platformy Spark. Następnie znajdź [współrzędnych maven](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) są zgodne z łącznikiem Cassandra Spark i dołączyć go do klastra. Zobacz ["Przekaż pakiet Maven lub Spark pakietu"](https://docs.databricks.com/user-guide/libraries.html) artykuł, aby dołączyć Biblioteka łącznika do klastra. Na przykład Współrzędna maven "Środowiska uruchomieniowego usługi Databricks w wersji 4.3," "Platformy Spark 2.3.1" i "jest Scala 2.11" `spark-cassandra-connector_2.11-2.3.1`

* **Biblioteka usługi Azure Cosmos DB Cassandra specyficzne dla interfejsu API:** — fabryka niestandardowego połączenia jest wymagana do skonfigurowania zasady ponawiania z łącznika Spark bazy danych Cassandra do interfejsu API usługi Azure Cosmos DB Cassandra. Dodaj `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0` [współrzędnych maven](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) na dołączanie biblioteki do klastra.

## <a name="sample-notebooks"></a>Przykładowe notesy

Wykaz usługi Azure Databricks [przykładowy notesów](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) są dostępne w repozytorium GitHub do pobrania. Te przykłady obejmują, jak nawiązać połączenie z interfejsem API usługi Azure Cosmos DB Cassandra z platformy Spark i wykonywać różne operacje CRUD na danych. Możesz również [zaimportować wszystkie notesy](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) do Twojej usługi Databricks klastra obszaru roboczego i uruchomimy ją. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Uzyskiwanie dostępu do interfejsu API usługi Azure Cosmos DB Cassandra z programów Spark Scala

Spark programy do uruchamiania zautomatyzowanych procesów w usłudze Azure Databricks są przesłane do klastra przy użyciu [skryptu spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html)) i zaplanowanych do uruchamiania za pośrednictwem zadań usługi Azure Databricks.

Poniżej przedstawiono, że linki pomagające w rozpoczęciu tworzenia programów Spark Scala do interakcji z interfejsem API usługi Azure Cosmos DB Cassandra.
* [Jak połączyć się z interfejsem API usługi Azure Cosmos DB Cassandra z programu Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Jak uruchomić program Spark Scala jako zautomatyzowane zadania w usłudze Azure Databricks](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [Pełna lista przykłady kodu dla pracy z interfejsem API bazy danych Cassandra](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Kolejne kroki

Rozpocznij od [utworzenia konta, bazy danych i tabeli interfejsu API Cassandra](create-cassandra-api-account-java.md) przy użyciu aplikacji w języku Java.
