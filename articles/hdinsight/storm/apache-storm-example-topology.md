---
title: Przykładowa topologia usługi Apache Storm w usłudze Azure HDInsight
description: Lista przykładowych topologii storm utworzonych i przetestowanych za pomocą aplikacji Apache Storm w programie HDInsight, w tym podstawowych topologii języka C# i Java oraz pracy z centrami zdarzeń.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 302ba583f11b15be98832316b1ea05c7f9be931f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530667"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Przykład apache Storm topologie i komponenty dla Apache Storm na HDInsight

Poniżej znajduje się lista przykładów utworzonych i obsługiwanych przez firmę Microsoft do użytku z [Apache Storm](https://storm.apache.org/) na HDInsight. Te przykłady obejmują wiele tematów, od tworzenia podstawowych topologii języka C# i Java do pracy z usługami platformy Azure, takimi jak usługi Event Hubs, Cosmos DB, baza danych SQL, [Apache HBase](https://hbase.apache.org/) w usłudze HDInsight i usługa Azure Storage. Niektóre przykłady pokazują również, jak pracować z platformą non-Azure, a nawet z technologiami innych firm, takimi jak SignalR i Socket.IO.

| Opis | Demonstracje | Język/struktura |
|:--- |:--- |:--- |
| [Napisz do usługi Azure Data Lake Storage z usługi Apache Storm](apache-storm-write-data-lake-store.md) |Pisanie do usługi Azure Data Lake Storage |Java |
| [Źródło wylewki i śruby centrum zdarzeń](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Źródło wylewki i śruby centrum zdarzeń |Java |
| [Opracowanie topologii opartych na javie dla Apache Storm na HDInsight][5797064f] |Maven |Java |
| [Tworzenie topologii języka C# dla usługi Apache Storm w programie HDInsight przy użyciu programu Visual Studio][16fce2d1] |Narzędzia HDInsight dla programu Visual Studio |C#, Java |
| [Przetwarzanie zdarzeń z usługi Azure Event Hubs za pomocą usługi Apache Storm w programie HDInsight (C#)][844d1d81] |Usługa Event Hubs |C# i Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) (Przetwarzanie zdarzeń usługi Azure Event Hubs przy użyciu systemu Storm w usłudze HDInsight — Java) |Usługa Event Hubs |Java |
| [Przetwarzaj dane z czujników pojazdu z centrów zdarzeń za pomocą aplikacji Apache Storm w programie HDInsight][246ee964] |Centra zdarzeń, usługa Cosmos DB, obiekt blob usługi Azure Storage (WASB) |C#, Java |
| [Wyodrębnianie, przekształcanie i ładowanie (ETL) z usługi Azure Event Hubs do apache HBase przy użyciu usługi Apache Storm w programie HDInsight][b4b68194] |Centra zdarzeń, Baza HBase |C# |
| [Projekt topologii burzy szablonu C# do pracy z usługami platformy Azure z usługi Apache Storm w programie HDInsight][ce0c02a2] |Centra zdarzeń, usługa Cosmos DB, baza danych SQL, baza danych HBase, SignalR |C#, Java |
| [Testy porównawcze skalowalności do odczytu z usługi Azure Event Hubs przy użyciu usługi Apache Storm w usłudze HDInsight][d6c540e3] |Przepływność wiadomości, centra zdarzeń, baza danych SQL |C#, Java |
| [Użyj Apache Kafka z Apache Storm na HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm czytanie i pisanie do Apache Kafka | Java |

> [!WARNING]  
> Przykłady języka C# na tej liście zostały pierwotnie utworzone i przetestowane przy za pomocą systemu Windows HDInsight i mogą nie działać poprawnie z klastrami HDInsight opartymi na systemie Linux. Klastry oparte na systemie Linux używają mono do uruchamiania kodu .NET i mogą mieć problemy ze zgodnością z strukturami i pakietami używanymi w przykładzie.
>
> Linux jest jedynym systemem operacyjnym używanym w wersji HDInsight 3.4 lub nowszym.

## <a name="python-only"></a>Tylko język Python

Zobacz [Używanie języka Python z apache storm na HDInsight](apache-storm-develop-python-topology.md) na przykład składników języka Python z topologią flux.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie i monitorowanie topologii usługi Apache Storm w usłudze Azure HDInsight](./apache-storm-quickstart.md)
* [Dowiedz się, jak wdrożyć i zarządzać topologie Apache Storm z Apache Storm na HDInsight][6eb0d3b8]

[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Dowiedz się, jak wdrażać topologie i zarządzać nimi za pomocą internetowego pulpitu nawigacyjnego apache storm i interfejsu użytkownika burzy lub narzędzi HDInsight Tools for Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Dowiedz się, jak utworzyć topologie burzy języka C# przy użyciu narzędzi HDInsight Tools for Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Dowiedz się, jak tworzyć topologie storm w języku Java, używając Maven, tworząc podstawową topologię wordcount."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Dowiedz się, jak odczytywać i zapisywać dane z usługi Azure Event Hubs za pomocą usługi Storm w usłudze HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Dowiedz się, jak używać topologii storm do odczytywania wiadomości z usługi Azure Event Hubs, odczytywania dokumentów z usługi Azure Cosmos DB w celu odwoływania się do danych i zapisywania danych w usłudze Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Kilka topologii, aby zademonstrować przepływność podczas odczytywania z usługi Azure Event Hubs i przechowywania w bazie danych SQL przy użyciu usługi Apache Storm w usłudze HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Dowiedz się, jak odczytywać dane z usługi Azure Event Hubs, agregować & przekształcać dane, a następnie przechowywać je w bazie danych HBase w usłudze HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Ten projekt zawiera szablony wylewek, śrub i topologii do interakcji z różnymi usługami platformy Azure, takich jak Usługi eventów, Usługi Cosmos DB i bazy danych SQL."
