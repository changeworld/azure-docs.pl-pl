---
title: Integracja z platformą Apache Spark — usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Integracja z platformą Apache Spark umożliwiające przesyłanie strumieniowe ze strukturą z usługi Event Hubs
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
ms.openlocfilehash: 605669a740663040ab7a167bf266fe1940123afc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343396"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integrowanie platformy Apache Spark z usługą Azure Event Hubs

Usługa Azure Event Hubs bezproblemowo integrują się ze [platformy Apache Spark](https://spark.apache.org/) umożliwiające tworzenie przesyłania strumieniowego aplikacji rozproszonych. Integracja [Spark Core](https://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html), i [przesyłanie strumieniowe ze strukturą](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Łącznika usługi Event Hubs dla platformy Apache Spark jest dostępna w [GitHub](https://github.com/Azure/azure-event-hubs-spark). Ta biblioteka jest również dostępny do użytku w projektach narzędzia Maven z [Maven Central Repository](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

W tym artykule opisano, jak utworzyć aplikację w języku ciągłe [usługi Azure Databricks](https://azure.microsoft.com/services/databricks/). W tym artykule jest używana usługa Azure Databricks, klastry Spark jest również dostępna za [HDInsight](../hdinsight/spark/apache-spark-overview.md).

W przykładzie w tym artykule użyto dwa notesy Scala: jednego zdarzenia z Centrum zdarzeń i inny wpis dla wysyłania zdarzeń do niego z powrotem do przesyłania strumieniowego.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Wystąpienie usługi Event Hubs. Jeśli nie masz, [utworzyć](event-hubs-create.md).
* [Usługi Azure Databricks](https://azure.microsoft.com/services/databricks/) wystąpienia. Jeśli nie masz, [utworzyć](../azure-databricks/quickstart-create-databricks-workspace-portal.md).
* [Utworzyć bibliotekę przy użyciu współrzędnych maven](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

Stream zdarzenia z Centrum zdarzeń przy użyciu następującego kodu:

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
Poniższy kod wysyła zdarzenia do Centrum zdarzeń za pomocą usługi batch Spark interfejsów API. Można również napisać zapytanie przesyłania strumieniowego do wysyłania zdarzeń do Centrum zdarzeń:

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

## <a name="next-steps"></a>Kolejne kroki

Teraz już wiesz, jak skonfigurować skalowalne, odporne na uszkodzenia strumienia za pomocą łącznika usługi Event Hubs dla platformy Apache Spark. Dowiedz się więcej o korzystaniu z usługi Event Hubs z przesyłanie strumieniowe ze strukturą i Spark Streaming postępując zgodnie z poniższych linków:

* [Przesyłanie strumieniowe ze strukturą i przewodnik integracji usługi Azure Event Hubs](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Przesyłania strumieniowego platformy Spark i przewodnik integracji usługi Event Hubs](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
