---
title: Przykład topologii Apache Storm w usłudze Azure HDInsight
description: Listę przykładowych topologii Storm utworzone i przetestowane za pomocą Storm Apache na HDInsight, takich jak podstawowe topologie C# i Java oraz pracy z usługą Event Hubs.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: b2407200b995ad956f233248f6cdd58488b583d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64688788"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Przykład topologii Apache Storm i składniki dla Storm Apache na HDInsight

Poniżej przedstawiono listę przykładów tworzone i konserwowane przez firmę Microsoft do użycia z usługą [Apache Storm](https://storm.apache.org/) na HDInsight. Te przykłady obejmują różne tematy od tworzenia podstawowych C# i topologii języka Java do pracy z usługami platformy Azure, takich jak centra zdarzeń, Cosmos DB, bazy danych SQL, [bazy danych Apache HBase](https://hbase.apache.org/) HDInsight i Azure Storage. Niektóre przykłady pokazują również jak pracować z technologii spoza platformy Azure lub nawet firm innych niż Microsoft, takich jak SignalR i biblioteki Socket.IO.

| Opis | Demonstracje | Języka/platformy |
|:--- |:--- |:--- |
| [Zapis do usługi Azure Data Lake Storage z systemu Apache Storm](apache-storm-write-data-lake-store.md) |Zapisywanie do usługi Azure Data Lake Storage |Java |
| [Źródło elementów Spout Centrum zdarzeń i elementu Bolt](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Źródło zdarzeń Centrum Spout i Bolt |Java |
| [Opracowywanie topologii opartych na języku Java dla usługi Storm Apache na HDInsight][5797064f] |Maven |Java |
| [Opracowywanie topologii języka C# dla Storm Apache na HDInsight przy użyciu programu Visual Studio][16fce2d1] |Narzędzia HDInsight Tools for Visual Studio |C#, Java |
| [Przetwarzania zdarzeń pochodzących z usługi Azure Event Hubs przy użyciu platformy Apache Storm w HDInsight (C#)][844d1d81] |Event Hubs |C# i Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) (Przetwarzanie zdarzeń usługi Azure Event Hubs przy użyciu systemu Storm w usłudze HDInsight — Java) |Event Hubs |Java |
| [Przetwarzanie danych czujnika pojazdu z usługi Event Hubs przy użyciu systemu Storm Apache na HDInsight][246ee964] |Zdarzenia obiektu Blob magazynu Azure Cosmos DB koncentratorów (WASB) |C#, Java |
| [Wyodrębniania, przekształcania i ładowania (ETL) z usługi Azure Event Hubs do bazy danych Apache HBase, przy użyciu systemu Storm Apache na HDInsight][b4b68194] |Usługa Event Hubs, bazy danych HBase |C# |
| [Szablon C# projektu topologii systemu Storm do pracy z usługami platformy Azure z systemu Apache Storm w HDInsight][ce0c02a2] |Zdarzenie Hubs usługi Cosmos DB SQL bazy danych HBase, SignalR |C#, Java |
| [Skalowalność testów porównawczych, podczas odczytu z usługi Azure Event Hubs przy użyciu systemu Storm Apache na HDInsight][d6c540e3] |Przepływność komunikatów, Event Hubs, SQL Database |C#, Java |
| [Korzystanie z języka Python w systemie Storm Apache na HDInsight](apache-storm-develop-python-topology.md) |Składników w języku Python z topologią strumienia |Python |
| [Platforma Apache Kafka za pomocą Storm Apache na HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm, Odczyt i zapis do platformy Apache Kafka | Java |

> [!WARNING]  
> Przykłady języka C# na tej liście początkowo zostały utworzone i przetestowane za pomocą HDInsight z systemem Windows i może nie działają prawidłowo z klastrami HDInsight opartych na systemie Linux. Opartą na systemie Linux klastrów używać platformy Mono, aby uruchomić kod .NET i może mieć problemy ze zgodnością ze strukturami i pakiety użytych w przykładzie.
>
> Linux jest jedynym systemem operacyjnym na HDInsight w wersji 3.4 lub nowszej.

### <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do systemu Apache Storm w usłudze HDInsight][2b8c3488]
* [Dowiedz się, jak wdrażanie topologii Apache Storm z platformą Apache Storm w HDInsight i zarządzanie][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Dowiedz się, jak utworzyć Apache Storm w klastrze HDInsight i korzystać z pulpitu nawigacyjnego Storm, aby wdrożyć przykładowe topologie."
[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Dowiedz się, jak wdrażanie topologii i zarządzanie nimi za pomocą opartego na sieci web Apache Storm pulpitu nawigacyjnego i interfejs użytkownika platformy Storm lub narzędzi HDInsight dla programu Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Dowiedz się, jak tworzenie topologii Storm języka C# za pomocą narzędzi HDInsight dla programu Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Dowiedz się, jak tworzenie topologii Storm w języku Java, przy użyciu narzędzia Maven, tworząc topologii wordcount podstawowe."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Dowiedz się, jak odczytywać i zapisywać dane z usługi Azure Event Hubs przy użyciu systemu Storm na HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Dowiedz się, jak odczytywać komunikaty z usługi Azure Event Hubs, przeczytaj dokumenty z usługi Azure Cosmos DB do odwoływania się do danych i zapisać dane do usługi Azure Storage za pomocą topologii systemu Storm."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Kilka topologii w celu zademonstrowania przepływności podczas odczytywania z usługi Azure Event Hubs i zapisywanie do usługi SQL Database przy użyciu systemu Storm Apache na HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Dowiedz się, jak odczytywać dane z usługi Azure Event Hubs, agregacji i przekształcać dane, a następnie zapisz go do bazy danych HBase na HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Ten projekt zawiera szablony dla elementów spout i bolt topologii w celu interakcji z różnymi usługami platformy Azure, takich jak centra zdarzeń, Cosmos DB i bazy danych SQL."

