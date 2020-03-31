---
title: Integracja z apache Spark — usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak zintegrować z Apache Spark, aby włączyć usługi przesyłania strumieniowego strukturalnego z centrami zdarzeń.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 4c4fd74e9123e1310be297a15090433d365d24cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76311686"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integracja platformy Apache Spark z centrum zdarzeń platformy Azure

Usługa Azure Event Hubs bezproblemowo integruje się z [platformą Apache Spark,](https://spark.apache.org/) aby umożliwić tworzenie rozproszonych aplikacji do przesyłania strumieniowego. Ta integracja obsługuje [spark Core,](https://spark.apache.org/docs/latest/rdd-programming-guide.html) [Spark Streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html)i [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Łącznik Centrum zdarzeń dla platformy Apache Spark jest dostępny w [usłudze GitHub.](https://github.com/Azure/azure-event-hubs-spark) Ta biblioteka jest również dostępna do użytku w projektach Maven z [Centralnego Repozytorium Maven.](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C)

W tym artykule opisano sposób tworzenia ciągłej aplikacji w [usłudze Azure Databricks](https://azure.microsoft.com/services/databricks/). W tym artykule użyto usługi Azure Databricks, klastry platformy Spark są również dostępne w systemie [HDInsight.](../hdinsight/spark/apache-spark-overview.md)

W tym artykule użyto dwóch notesów Scala: jednego do przesyłania strumieniowego zdarzeń z centrum zdarzeń, a drugiego do wysyłania zdarzeń z powrotem do niego.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli go nie masz, [utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Wystąpienie centrum zdarzeń. Jeśli go nie masz, [utwórz jeden plik](event-hubs-create.md).
* Wystąpienie [usługi Azure Databricks.](https://azure.microsoft.com/services/databricks/) Jeśli go nie masz, [utwórz jeden plik](../azure-databricks/quickstart-create-databricks-workspace-portal.md).
* [Utwórz bibliotekę przy użyciu współrzędnych maven:](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package) `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

Przesyłaj strumieniowo zdarzenia z centrum zdarzeń przy użyciu następującego kodu:

```scala
import org.apache.spark.eventhubs._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()
val eventhubs = reader.select($"body" cast "string")

eventhubs.writeStream
  .format("console")
  .outputMode("append")
  .start()
  .awaitTermination()
```
Poniższy kod wysyła zdarzenia do centrum zdarzeń za pomocą interfejsów API partii platformy Spark. Można również napisać kwerendę przesyłania strumieniowego, aby wysłać zdarzenia do centrum zdarzeń:

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build
val ehConf = EventHubsConf(connectionString)

// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified event hub.
val df = spark.range(200).select(bodyColumn)
df.write
  .format("eventhubs")
  .options(ehConf.toMap)
  .save() 
```

## <a name="next-steps"></a>Następne kroki

Teraz wiesz, jak skonfigurować skalowalny, odporny na uszkodzenia strumień przy użyciu łącznika centrów zdarzeń dla platformy Spark apache. Dowiedz się więcej o korzystaniu z centrów zdarzeń z ustrukturyzowanym strumieniowaniem strumieniowym i strumieniowaniem w ramach platformy Spark, wykonując następujące łącza:

* [Ustrukturyzowany program przesyłania strumieniowego + przewodnik po integracji centrów zdarzeń platformy Azure](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Przewodnik po przesyłanie strumieniowe platformy Spark + centrum zdarzeń](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
