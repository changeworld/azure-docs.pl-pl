---
title: Dostęp do interfejsu API Cassandra usługi Azure Cosmos DB z usługi Azure Databricks
description: W tym artykule opisano, jak pracować z interfejsem API Cassandra usługi Azure Cosmos DB z usługi Azure Databricks.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 37a06b19285c1196b5d87830ea176d4bd0d4eade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60894015"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Dostęp do danych interfejsu API usługi Azure Cosmos DB Cassandra z usługi Azure Databricks

W tym artykule opisano, jak pracować z interfejsem API Cassandra usługi Azure Cosmos DB z platformy Spark on [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks).

## <a name="prerequisites"></a>Wymagania wstępne

* [Aprowizuj konto interfejsu API usługi Azure Cosmos DB Cassandra](create-cassandra-dotnet.md#create-a-database-account)

* [Zapoznaj się z podstawami łączenia się z interfejsem API Cassandra usługi Azure Cosmos DB](cassandra-spark-generic.md)

* [Aprowizuj klaster usługi Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Przejrzyj przykłady kodu do pracy z interfejsem API Cassandra](cassandra-spark-generic.md#next-steps)

* [Użyj cqlsh do sprawdzania poprawności, jeśli wolisz](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Konfiguracja wystąpienia interfejsu API Cassandra dla łącznika Cassandra:**

  Łącznik dla interfejsu API Cassandra wymaga szczegóły połączenia Cassandra, które mają być inicjowane jako część kontekstu iskry. Po uruchomieniu notesu Databricks kontekst iskry jest już zainicjowany i nie zaleca się jego zatrzymywania i ponownego inicjowania. Jednym z rozwiązań jest dodanie konfiguracji wystąpienia interfejsu API Cassandra na poziomie klastra w konfiguracji iskry klastra. Jest to działanie jednorazowe na klaster. Dodaj następujący kod do konfiguracji platformy Spark jako parę wartości klucza oddzielonego spacją:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Dodawanie wymaganych zależności

* **Łącznik Cassandra Spark:** — aby zintegrować interfejs API Cassandra usługi Azure Cosmos DB z platformą Spark, łącznik Cassandra powinien być dołączony do klastra usługi Azure Databricks. Aby dołączyć klaster:

  * Przejrzyj wersję środowiska wykonawczego Databricks, wersję platformy Spark. Następnie znajdź [współrzędne maven,](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) które są zgodne ze złączem Cassandra Spark i dołącz je do klastra. Zobacz ["Przekaż pakiet Maven lub pakiet Platformy Spark"](https://docs.databricks.com/user-guide/libraries.html) artykuł, aby dołączyć bibliotekę łączników do klastra. Na przykład współrzędna maven dla "Databricks Runtime w wersji 4.3", "Spark 2.3.1" i "Scala 2.11" jest`spark-cassandra-connector_2.11-2.3.1`

* **Biblioteka specyficzne dla interfejsu API usługi Azure Cosmos DB Cassandra:** — do skonfigurowania zasad ponawiania próby z łącznika Cassandra Spark do interfejsu API cassandra usługi Azure Cosmos DB. Dodaj `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0` [współrzędne maven,](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) aby dołączyć bibliotekę do klastra.

## <a name="sample-notebooks"></a>Przykładowe notesy

Lista [przykładowych notesów](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) usługi Azure Databricks jest dostępna w repozytorium Usługi GitHub do pobrania. Te przykłady obejmują jak połączyć się z interfejsem API Cassandra usługi Azure Cosmos DB z platformy Spark i wykonywać różne operacje CRUD na danych. Można również [zaimportować wszystkie notesy](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) do obszaru roboczego klastra Databricks i uruchomić go. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Uzyskiwanie dostępu do interfejsu API cassandra usługi Azure Cosmos DB z programów Spark Scala

Programy platformy Spark, które mają być uruchamiane jako zautomatyzowane procesy na platformie Azure Databricks są przesyłane do klastra przy użyciu [spark-submit)](https://spark.apache.org/docs/latest/submitting-applications.html)i zaplanowane do uruchomienia za pośrednictwem zadań usługi Azure Databricks.

Poniżej znajdują się łącza ułatwiające rozpoczęcie tworzenia programów Spark Scala do interakcji z interfejsem API Cassandra usługi Azure Cosmos DB Cassandra.
* [Jak połączyć się z interfejsem API Cassandra usługi Azure Cosmos DB z programu Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Jak uruchomić program Spark Scala jako zautomatyzowane zadanie na platformie Azure Databricks](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [Pełna lista przykładów kodu do pracy z api Cassandra](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do [tworzenia konta interfejsu API Cassandra, bazy danych i tabeli](create-cassandra-api-account-java.md) przy użyciu aplikacji Java.
