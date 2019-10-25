---
title: Przykładowe topologie Apache Storm w usłudze Azure HDInsight
description: Lista przykładowych topologii burzy utworzonych i przetestowanych przy użyciu Apache Storm C# w usłudze HDInsight, w tym topologii podstawowa i Java, a także praca z Event Hubs.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 831d3a187a4388a429c6fabb1bb0e0ec6dd584cc
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72800044"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Przykład Apache Storm topologie i składniki Apache Storm w usłudze HDInsight

Poniżej znajduje się Lista przykładów utworzonych i obsługiwanych przez firmę Microsoft do użycia z usługą [Apache Storm](https://storm.apache.org/) w usłudze HDInsight. Te przykłady obejmują różne tematy, od tworzenia topologii Basic C# i Java do pracy z usługami platformy Azure, takimi jak Event Hubs, Cosmos DB, SQL Database, [Apache HBase](https://hbase.apache.org/) w usłudze HDInsight i Azure Storage. W niektórych przykładach przedstawiono również sposób pracy z platformą inną niż Azure, a nawet technologiami nienależącymi do firmy Microsoft, takimi jak sygnalizujący i Socket.IO.

| Opis | Pokazuje | Język/struktura |
|:--- |:--- |:--- |
| [Zapisz do Azure Data Lake Storage z Apache Storm](apache-storm-write-data-lake-store.md) |Zapisywanie w Azure Data Lake Storage |Java |
| [Elementu Spout i źródło piorunu centrum zdarzeń](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Źródło dla elementu Spout i błyskawicy centrum zdarzeń |Java |
| [Tworzenie topologii opartych na języku Java dla Apache Storm w usłudze HDInsight][5797064f] |Maven |Java |
| [Tworzenie C# topologii dla Apache Storm w usłudze HDInsight przy użyciu programu Visual Studio][16fce2d1] |Narzędzia HDInsight Tools for Visual Studio |C#, Java |
| [Przetwarzaj zdarzenia z usługi Azure Event Hubs przy użyciu Apache StormC#w usłudze HDInsight ()][844d1d81] |Centra zdarzeń |C#i Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) (Przetwarzanie zdarzeń usługi Azure Event Hubs przy użyciu systemu Storm w usłudze HDInsight — Java) |Centra zdarzeń |Java |
| [Przetwarzanie danych z czujnika pojazdu z Event Hubs przy użyciu Apache Storm w usłudze HDInsight][246ee964] |Event Hubs, Cosmos DB, Azure Storage Blob (WASB) |C#, Java |
| [Wyodrębnianie, przekształcanie i ładowanie (ETL) z usługi Azure Event Hubs do platformy Apache HBase przy użyciu Apache Storm w usłudze HDInsight][b4b68194] |Event Hubs, HBase |C# |
| [Szablon C# projektu topologii burzy na potrzeby pracy z usługami platformy Azure z Apache Storm w usłudze HDInsight][ce0c02a2] |Event Hubs, Cosmos DB, SQL Database, HBase, sygnalizujący |C#, Java |
| [Testy wydajnościowe do czytania z usługi Azure Event Hubs przy użyciu Apache Storm w usłudze HDInsight][d6c540e3] |Przepływność komunikatów, Event Hubs, SQL Database |C#, Java |
| [Używanie Apache Kafka z Apache Storm w usłudze HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm odczytywanie i zapisywanie do Apache Kafka | Java |

> [!WARNING]  
> C# Przykłady na tej liście zostały pierwotnie utworzone i przetestowane przy użyciu usługi HDInsight opartej na systemie Windows i mogą nie funkcjonować prawidłowo w przypadku klastrów usługi HDInsight opartych na systemach Linux. Klastry oparte na systemie Linux używają programu mono do uruchamiania kodu platformy .NET i mogą mieć problemy ze zgodnością z platformami i pakietami używanymi w tym przykładzie.
>
> System Linux jest jedynym systemem operacyjnym używanym w usłudze HDInsight w wersji 3,4 lub nowszej.

## <a name="python-only"></a>Tylko język Python

Zobacz [Używanie języka Python z Apache Storm w usłudze HDInsight](apache-storm-develop-python-topology.md) , aby zapoznać się z przykładem składników języka Python z topologią strumienia.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie i monitorowanie topologii Apache Storm w usłudze Azure HDInsight](./apache-storm-quickstart.md)
* [Dowiedz się, jak wdrażać topologie Apache Storm i zarządzać nimi za pomocą Apache Storm w usłudze HDInsight][6eb0d3b8]


[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Dowiedz się, jak wdrażać topologie za pomocą pulpitu nawigacyjnego Apache Storm opartego na sieci Web i interfejsu użytkownika burzy oraz narzędzi HDInsight Tools for Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Dowiedz się, C# jak tworzyć topologie burzy przy użyciu narzędzi HDInsight Tools for Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Dowiedz się, jak tworzyć topologie burzy w języku Java przy użyciu Maven, tworząc podstawową topologię WORDCOUNT."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Dowiedz się, jak odczytywać i zapisywać dane z usługi Azure Event Hubs przy użyciu burzy w usłudze HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Dowiedz się, jak używać topologii burzy do odczytywania wiadomości z usługi Azure Event Hubs, odczytywania dokumentów z Azure Cosmos DB danych i zapisywania ich w usłudze Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Kilka topologii do zademonstrowania przepływności podczas czytania z platformy Azure Event Hubs i przechowywania do SQL Database przy użyciu Apache Storm w usłudze HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Dowiedz się, jak odczytywać dane z usługi Azure Event Hubs, agregować & przekształcać dane, a następnie przechować je w usłudze HBase w usłudze HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Ten projekt zawiera szablony dla elementy Spout, piorunów i topologii do współpracy z różnymi usługami platformy Azure, takimi jak Event Hubs, Cosmos DB i SQL Database."

