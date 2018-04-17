---
title: Przykład topologii Apache Storm w usłudze HDInsight | Dokumentacja firmy Microsoft
description: Listę przykładowych topologii Storm utworzone i przetestowane z systemu Apache Storm w usłudze HDInsight tym podstawowe topologii C# i Java i pracy z usługą Event Hubs.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f9b1bdff-5928-4705-a76d-52fd200917cb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: larryfr
ms.openlocfilehash: f633797147b2090680ebb5ea11f60271addc7f52
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="example-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Przykładowych topologii Storm i składniki Apache STORM w usłudze HDInsight

Poniżej znajduje się lista przykłady tworzone i obsługiwane przez firmę Microsoft do użycia z systemu Apache Storm w usłudze HDInsight. Te przykłady obejmują różne tematy tworzenia podstawowych C# i topologii Java do pracy z usługami Azure, takich jak usługi Event Hubs, rozwiązania Cosmos bazy danych, bazy danych SQL, HBase HDInsight i usługi Azure Storage. Również przykłady pokazują, jak pracować z technologii innych niż Azure lub nawet innych niż Microsoft, takich jak SignalR i użyciu biblioteki Socket.IO.

| Opis | Pokazuje | Język/Framework |
|:--- |:--- |:--- |
| [Zapis do usługi Azure Data Lake Store z systemu Apache Storm](apache-storm-write-data-lake-store.md) |Zapisywanie do usługi Azure Data Lake Store |Java |
| [Elementów Spout Centrum zdarzeń i Bolt źródła](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Źródło Spout Centrum zdarzeń i Bolt |Java |
| [Opracowywanie topologii opartych na języku Java Apache STORM w usłudze HDInsight][5797064f] |Maven |Java |
| [Tworzenie topologii C# dla Apache Storm w usłudze HDInsight przy użyciu programu Visual Studio][16fce2d1] |HDInsight Tools for Visual Studio |C#, Java |
| [Zdarzenia procesu z usługi Azure Event Hubs z systemu Storm w usłudze HDInsight (C#)][844d1d81] |Event Hubs |C# i Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) (Przetwarzanie zdarzeń usługi Azure Event Hubs przy użyciu systemu Storm w usłudze HDInsight — Java) |Event Hubs |Java |
| [Przetwarzania danych czujnika vehicle z usługi Event Hubs za pomocą Storm w usłudze HDInsight][246ee964] |Zdarzenie koncentratory, DB rozwiązania Cosmos obiektu Blob magazynu Azure (WASB) |C#, Java |
| [Wyodrębniania, przekształcania i ładowania (ETL) z usługi Azure Event Hubs HBase i korzystanie z systemu Storm w usłudze HDInsight][b4b68194] |Centra zdarzeń, HBase |C# |
| [Szablon projektu o topologii Storm C# do pracy z usługami Azure Storm w usłudze HDInsight][ce0c02a2] |Zdarzenie koncentratory, DB rozwiązania Cosmos SQL bazy danych, HBase, SignalR |C#, Java |
| [Wzorce skalowalność do odczytywania z usługi Azure Event Hubs za pomocą Storm w usłudze HDInsight][d6c540e3] |Komunikat przepływności usługi Event Hubs bazy danych SQL |C#, Java |
| [Python za pomocą Storm w usłudze HDInsight](apache-storm-develop-python-topology.md) |Składniki Python z topologią strumienia |Python |
| [Kafka za pomocą Storm w usłudze HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm odczytywanie i zapisywanie do Apache Kafka | Java |

### <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do systemu Apache Storm w usłudze HDInsight][2b8c3488]
* [Dowiedz się, jak wdrożyć i zarządzanie topologiami Storm z systemu Storm w usłudze HDInsight][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Dowiedz się, jak utworzyć platformy Storm w klastrze usługi HDInsight i przykładowe topologie wdrażania za pomocą pulpitu nawigacyjnego Storm."
[6eb0d3b8]:apache-storm-deploy-monitor-topology.md "Informacje o sposobie wdrażania i zarządzania nimi topologii za pomocą pulpitu nawigacyjnego Storm opartej na sieci web i interfejsu użytkownika platformy Storm lub narzędzia HDInsight Tools for Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Informacje o sposobie tworzenia topologii Storm C# za pomocą narzędzi HDInsight Tools for Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Informacje o sposobie tworzenia topologii Storm w języku Java, za pomocą programu Maven, tworząc topologii wordcount podstawowe."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Dowiedz się, jak odczytywania i zapisywania danych z usługi Azure Event Hubs z systemu Storm w usłudze HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Dowiedz się, jak odczytywanie wiadomości usługi Azure Event Hubs przy użyciu topologii Storm odczytywać z bazy danych usługi Azure rozwiązania Cosmos dla odwołania do danych i zapisać dane do magazynu Azure."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Kilku topologiach aby zademonstrować przepływności podczas odczytywania z usługi Azure Event Hubs i zapisywanie do bazy danych SQL za pomocą platformy Apache Storm w usłudze HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Dowiedz się, jak odczytywać dane z usługi Azure Event Hubs, łączny & przekształcania danych, a następnie zapisz go do bazy danych HBase w usłudze HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Ten projekt zawiera szablony elementach spout, elementów bolt i topologii do interakcji z różnych usług Azure, takich jak usługi Event Hubs, rozwiązania Cosmos bazę danych i bazy danych SQL."

