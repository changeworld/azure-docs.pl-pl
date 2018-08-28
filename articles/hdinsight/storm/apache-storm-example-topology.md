---
title: Przykład topologii Apache Storm w usłudze Azure HDInsight
description: Listę przykładowych topologii Storm utworzone i przetestowane za pomocą Storm Apache na HDInsight, takich jak podstawowe topologie C# i Java oraz pracy z usługą Event Hubs.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 6f88011bcc3d3c7a97e12a7c96da0b5e3280ad05
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43044930"
---
# <a name="example-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Przykładowych topologii Storm i składniki dla Storm Apache na HDInsight

Oto lista przykłady tworzone i konserwowane przez firmę Microsoft do użycia z usługą Apache Storm w HDInsight. Te przykłady obejmują różne tematy od tworzenia podstawowych języka C# i topologii języka Java do pracy z usługami platformy Azure, takich jak centra zdarzeń, Cosmos DB, bazy danych SQL, bazy danych HBase HDInsight i Azure Storage. Niektóre przykłady pokazują również jak pracować z technologii spoza platformy Azure lub nawet firm innych niż Microsoft, takich jak SignalR i biblioteki Socket.IO.

| Opis | Pokazuje | Języka/platformy |
|:--- |:--- |:--- |
| [Zapis do usługi Azure Data Lake Store z platformą Apache Storm](apache-storm-write-data-lake-store.md) |Zapisywanie w usłudze Azure Data Lake Store |Java |
| [Źródło elementów Spout Centrum zdarzeń i elementu Bolt](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Źródło zdarzeń Centrum Spout i Bolt |Java |
| [Opracowywanie topologii opartych na języku Java dla usługi Storm Apache na HDInsight][5797064f] |Maven |Java |
| [Opracowywanie topologii języka C# dla Storm Apache na HDInsight przy użyciu programu Visual Studio][16fce2d1] |HDInsight Tools for Visual Studio |C#, Java |
| [Przetwarzanie zdarzeń z usługi Azure Event Hubs przy użyciu systemu Storm w HDInsight (C#)][844d1d81] |Event Hubs |C# i Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) (Przetwarzanie zdarzeń usługi Azure Event Hubs przy użyciu systemu Storm w usłudze HDInsight — Java) |Event Hubs |Java |
| [Przetwarzanie danych czujnika pojazdu z usługi Event Hubs przy użyciu systemu Storm na HDInsight][246ee964] |Zdarzenia obiektu Blob magazynu Azure Cosmos DB koncentratorów (WASB) |C#, Java |
| [Wyodrębniania, przekształcania i ładowania (ETL) z usługi Azure Event Hubs do bazy danych HBase na HDInsight przy użyciu technologii Storm][b4b68194] |Usługa Event Hubs, bazy danych HBase |C# |
| [Szablon projektu o topologii Storm języka C# do pracy z usługami platformy Azure z systemem Storm w HDInsight][ce0c02a2] |Zdarzenie Hubs usługi Cosmos DB SQL bazy danych HBase, SignalR |C#, Java |
| [Skalowalność testów porównawczych, podczas odczytu z usługi Azure Event Hubs przy użyciu systemu Storm na HDInsight][d6c540e3] |Przepływność komunikatów, Event Hubs, SQL Database |C#, Java |
| [Za pomocą języka Python z systemu Storm w HDInsight](apache-storm-develop-python-topology.md) |Składników w języku Python z topologią strumienia |Python |
| [Używanie usługi Kafka z systemem Storm w HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm, Odczyt i zapis do platformy Apache Kafka | Java |

> [!WARNING]
> Przykłady języka C# na tej liście początkowo zostały utworzone i przetestowane za pomocą HDInsight z systemem Windows i może nie działają prawidłowo z klastrami HDInsight opartych na systemie Linux. Opartą na systemie Linux klastrów używać platformy Mono, aby uruchomić kod .NET i może mieć problemy ze zgodnością ze strukturami i pakiety użytych w przykładzie.
>
> Linux jest jedynym systemem operacyjnym na HDInsight w wersji 3.4 lub nowszej.

### <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do systemu Apache Storm w usłudze HDInsight][2b8c3488]
* [Dowiedz się, jak wdrażanie topologii Storm i zarządzanie przy użyciu systemu Storm w HDInsight][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Dowiedz się, jak utworzyć platformy Storm w klastrze HDInsight i korzystać z pulpitu nawigacyjnego Storm, aby wdrożyć przykładowe topologie."
[6eb0d3b8]:apache-storm-deploy-monitor-topology.md "Dowiedz się, jak wdrażanie topologii i zarządzanie nimi za pomocą pulpit nawigacyjny platformy Storm opartej na sieci web i interfejs użytkownika platformy Storm lub narzędzi HDInsight Tools for Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Dowiedz się, jak tworzenie topologii Storm języka C# za pomocą narzędzi HDInsight dla programu Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Dowiedz się, jak tworzenie topologii Storm w języku Java, przy użyciu narzędzia Maven, tworząc topologii wordcount podstawowe."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Dowiedz się, jak odczytywać i zapisywać dane z usługi Azure Event Hubs przy użyciu systemu Storm na HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Dowiedz się, jak odczytywać komunikaty z usługi Azure Event Hubs, przeczytaj dokumenty z usługi Azure Cosmos DB do odwoływania się do danych i zapisać dane do usługi Azure Storage za pomocą topologii systemu Storm."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Kilka topologii w celu zademonstrowania przepływności podczas odczytywania z usługi Azure Event Hubs i zapisywanie do usługi SQL Database przy użyciu systemu Storm Apache na HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Dowiedz się, jak odczytywać dane z usługi Azure Event Hubs, agregacji i przekształcać dane, a następnie zapisz go do bazy danych HBase na HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Ten projekt zawiera szablony dla elementów spout i bolt topologii w celu interakcji z różnymi usługami platformy Azure, takich jak centra zdarzeń, Cosmos DB i bazy danych SQL."

