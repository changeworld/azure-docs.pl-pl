---
title: Przechwytywanie zdarzeń przesyłania strumieniowego — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie funkcji przechwytywania, która pozwala na Przechwytywanie zdarzeń przesyłanych strumieniowo za pomocą usługi Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: c166f4cace6a8cc25b36a84f4614033801e69a51
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265014"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Przechwytywanie zdarzeń za pomocą usługi Azure Event Hubs w usłudze Azure Blob Storage lub magazynu usługi Azure Data Lake
Usługa Azure Event Hubs umożliwia automatyczne przechwytywanie danych przesyłanych strumieniowo w Event Hubs w usłudze [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) lub w ramach wybranego konta [Azure Data Lake Storage generacji 1 lub generacji 2](https://azure.microsoft.com/services/data-lake-store/) , z dodatkową elastycznością określania interwału czasu lub rozmiaru. Konfigurowanie przechwytywania jest szybkie, nie ma kosztów administracyjnych do uruchomienia i jest automatycznie skalowane przy użyciu [jednostek przepływności](event-hubs-scalability.md#throughput-units)Event Hubs. Funkcja przechwytywania usługi Event Hubs jest najprostszym sposobem ładowania danych strumieniowych na platformę Azure i pozwala skoncentrować się na przetwarzaniu danych zamiast na przechwytywania danych.

Funkcja przechwytywania usługi Event Hubs pozwala przetwarzać potoki w czasie rzeczywistym i opartych na partiach, w tym samym strumieniu. Oznacza to, że możesz tworzyć rozwiązania, które wzrostu z Twoimi potrzebami wraz z upływem czasu. Czy tworzysz systemów opartych na procesorze batch już dzisiaj za pomocą śledzenia do przyszłego przetwarzania w czasie rzeczywistym, które chcesz dodać wydajne ścieżki nieaktywnej do istniejącego rozwiązania w czasie rzeczywistym, funkcja przechwytywania usługi Event Hubs sprawia, że praca łatwiejsze danych przesyłanych strumieniowo.


## <a name="how-event-hubs-capture-works"></a>Jak działa funkcja przechwytywania usługi Event Hubs

Usługa Event Hubs jest trwały bufor czasu przechowywania dla przychodzących danych telemetrycznych podobne do rozproszonej dziennika. Klucz do skalowania w Event Hubs jest [modelem odbiorcy partycjonowanym](event-hubs-scalability.md#partitions). Każda partycja jest niezależna segmentu danych i jest używane niezależnie. Wraz z upływem czasu te dane wieku, na podstawie okresu przechowywania można skonfigurować. W wyniku podanego zdarzenia koncentratora nigdy nie pobiera "zbyt pełnej."

Przechwytywanie Event Hubs umożliwia określenie własnego konta i kontenera usługi Azure Blob Storage, a także konta Azure Data Lake Storage, które są używane do przechowywania przechwyconych danych. Te konta można w tym samym regionie, co Centrum zdarzeń lub w innym regionie znajdującym się dodanie do elastyczność funkcji przechwytywania usługi Event Hubs.

Przechwycone dane są zapisywane w formacie [Apache Avro][Apache Avro] : kompaktowy, szybki format binarny, który zapewnia rozbudowane struktury danych z wbudowanym schematem. Ten format jest powszechnie używany w ekosystemie usługi Hadoop, usługi Stream Analytics i Azure Data Factory. Więcej informacji na temat pracy z Avro jest dostępna w dalszej części tego artykułu.

### <a name="capture-windowing"></a>Przechwytywanie obsługi okien

Funkcja przechwytywania usługi Event Hubs umożliwia konfigurowanie przedziału do kontrolowania przechwytywania. To okno jest minimalny rozmiar i Konfiguracja czasu za pomocą "pierwszy wins zasad," oznacza, że to pierwszy wyzwalacz, napotkano powoduje, że operacji przechwytywania. W przypadku 15 minutowy 100 MB okna Przechwytywanie i wysłać 1 MB na sekundę, wyzwalacze okna rozmiar przed w danym przedziale czasu. Każda partycja przechwytuje niezależnie i zapisuje w czasie przechwytywania, ukończone blokowego obiektu blob o nazwie czas napotkano interwał przechwytywania. Konwencja nazewnictwa storage jest następująca:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Należy pamiętać, że wartości daty są dopełniane zerami; przykład nazwy pliku może być:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

W przypadku, gdy obiekt BLOB usługi Azure Storage jest tymczasowo niedostępny, przechwytywanie Event Hubs zachowa dane dla okresu przechowywania danych skonfigurowanego w centrum zdarzeń i zapisze dane po ponownym udostępnieniu konta magazynu.

### <a name="scaling-to-throughput-units"></a>Skalowanie do wszystkich jednostek przepływności

Ruch Event Hubs jest kontrolowany przez [jednostki przepływności](event-hubs-scalability.md#throughput-units). Pojedyncza jednostka przepływności umożliwia 1 MB na sekundę lub 1000 zdarzeń na sekundę transferu danych przychodzących i dwa razy ilość danych wychodzących. Standardowe Event Hubs można skonfigurować przy użyciu jednostek przepływności 1-20 i można zakupić więcej dzięki [żądaniu zwiększenia obsługi][support request]przydziału. Użycie jednostek przepływności zakupionych jest ograniczane. Funkcja przechwytywania usługi Event Hubs kopiuje dane bezpośrednio z pamięci wewnętrznej usługi Event Hubs, pomijanie limity przydziału ruchu wychodzącego jednostek przepływności i zapisywanie swojej ruchu wychodzącego dla innych czytników przetwarzania, takich jak Stream Analytics lub Spark.

Po skonfigurowaniu usługi Event Hubs Capture jest uruchamiany automatycznie po wysłaniu pierwszego zdarzenia i będzie kontynuował działanie. Aby ułatwić Twojej przetwarzania transmisji dla klientów dowiedzieć się, że proces działa usługa Event Hubs zapisuje pustych plików, czy nie ma żadnych danych. Ten proces obejmuje tempa przewidywalny i znaczników, który może przekazywać procesorów usługi batch.

## <a name="setting-up-event-hubs-capture"></a>Konfigurowanie przechwytywania usługi Event Hubs

Funkcję przechwytywania można skonfigurować w czasie tworzenia centrum zdarzeń przy użyciu [Azure Portal](https://portal.azure.com)lub szablonów Azure Resource Manager. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Włącz przechwytywanie Event Hubs przy użyciu Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Tworzenie przestrzeni nazw Event Hubs z centrum zdarzeń i Włączanie funkcji przechwytywania przy użyciu szablonu Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Eksplorowanie plików przechwyconej i pracą z nimi przy użyciu systemu Avro

Funkcja przechwytywania usługi Event Hubs tworzy pliki w formacie Avro, zgodnie z instrukcjami na przedział czasu skonfigurowane. Te pliki można wyświetlić w dowolnym narzędziu, takim jak [Eksplorator usługi Azure Storage][Azure Storage Explorer]. Możesz pobrać pliki lokalnie w celu pracy z nimi.

Pliki tworzone przez przechwytywania usługi Event Hubs mają następujące schemacie Avro:

![Schemat Avro][3]

Łatwym sposobem eksplorowania plików Avro jest użycie [Narzędzia Avro][Avro Tools] w postaci jar. Możesz również użyć funkcji [drążenia Apache][Apache Drill] w celu uzyskania uproszczonego środowiska opartego na języku SQL lub [Apache Spark][Apache Spark] , aby wykonać złożone przetwarzanie rozproszone dla pozyskiwanych danych. 

### <a name="use-apache-drill"></a>Korzystanie z funkcji drążenia Apache

[Apache drążenie][Apache Drill] to "otwarty aparat zapytań SQL" "open source" do eksploracji danych Big Data ", który może wykonywać zapytania o dane strukturalne i częściowo strukturalne wszędzie tam, gdzie się znajduje. Aparat może działać jako autonomiczny węzeł lub jako ogromny klaster w celu uzyskania doskonałej wydajności.

Dostępna jest Natywna obsługa magazynu obiektów blob platformy Azure, która ułatwia wykonywanie zapytań dotyczących danych w pliku Avro, zgodnie z opisem w dokumentacji:

[Funkcja drążenia Apache: wtyczka Blob Storage platformy Azure][Apache Drill: Azure Blob Storage Plugin]

Aby łatwo zbadać przechwycone pliki, możesz utworzyć i wykonać maszynę wirtualną z włączoną funkcją drążenia oprogramowania Apache za pośrednictwem kontenera, aby uzyskać dostęp do usługi Azure Blob Storage:

https://github.com/yorek/apache-drill-azure-blob

Pełny kompletny przykład jest dostępny w repozytorium przesyłania strumieniowego w skali:

[Przesyłanie strumieniowe na dużą skalę: Przechwytywanie Event Hubs]

### <a name="use-apache-spark"></a>Użyj Apache Spark

[Apache Spark][Apache Spark] to "ujednolicony aparat analityczny do przetwarzania danych na dużą skalę". Obsługuje on różne języki, w tym SQL, i może łatwo uzyskać dostęp do usługi Azure Blob Storage. Istnieje kilka opcji uruchamiania Apache Spark na platformie Azure, a każda z nich zapewnia łatwy dostęp do usługi Azure Blob Storage:

- [HDInsight: pliki adresów w usłudze Azure Storage][HDInsight: Address files in Azure storage]
- [Azure Databricks: Magazyn obiektów blob platformy Azure][Azure Databricks: Azure Blob Storage]
- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/spark-job) 

### <a name="use-avro-tools"></a>Korzystanie z narzędzi Avro

[Narzędzia Avro][Avro Tools] są dostępne jako pakiet jar. Po pobraniu pliku JAR można zobaczyć schemat określonego pliku Avro, uruchamiając następujące polecenie:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

To polecenie zwraca

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Aby przekonwertować plik do formatu JSON i wykonywać inne procesy przetwarzania umożliwia także narzędzia Avro.

Aby przeprowadzić bardziej zaawansowane przetwarzanie, Pobierz i zainstaluj Avro z dowolnie wybranych platform. W czasie tego pisania dostępne są implementacje języka C, C++, c\#, Java, NodeJS, Perl, php, Python i Ruby.

Oprogramowanie Apache Avro zakończyło się Wprowadzenie przewodników dla [języków Java][Java] i [Python][Python]. Możesz również przeczytać artykuł [wprowadzenie do Event Hubs przechwytywania](event-hubs-capture-python.md) .

## <a name="how-event-hubs-capture-is-charged"></a>Jak opłata przechwytywania usługi Event Hubs

Funkcja przechwytywania usługi Event Hubs jest mierzone w podobny sposób do wszystkich jednostek przepływności: jako dodatkowy co godzinę. Opłata jest wprost proporcjonalna do liczby jednostek przepływności zakupionych dla przestrzeni nazw. Jednostki przepływności są zwiększyć i zmniejszyć, liczniki przechwytywania usługi Event Hubs zwiększyć i zmniejszyć do zapewnienia wydajności dopasowania. Liczniki wystąpić w połączeniu sekcją. Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/). 

Należy pamiętać, że Przechwytywanie nie zużywa limitu przydziału ruchu wychodzącego, ponieważ jest rozliczany osobno. 

## <a name="integration-with-event-grid"></a>Integracja z usługą Event Grid 

Możesz utworzyć subskrypcję usługi Azure Event Grid z przestrzeni nazw usługi Event Hubs jako źródła. W poniższym samouczku pokazano, jak utworzyć Event Grid subskrypcję z centrum zdarzeń jako źródło i Azure Functions aplikację jako ujścia: [proces i migrowanie przechwyconych danych Event Hubs do SQL Data Warehouse przy użyciu Event Grid i Azure Functions](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Następne kroki
Funkcja przechwytywania usługi Event Hubs jest najprostszym sposobem pobierania danych do platformy Azure. Za pomocą usługi Azure Data Lake, Azure Data Factory i Azure HDInsight, można wykonać przetwarzania wsadowego i innych analiz, przy użyciu dobrze znanych narzędzi i platform wybrane w dowolnej skali konieczne.

Dowiedz się, jak włączyć tę funkcję przy użyciu szablonu Azure Portal i Azure Resource Manager:

- [Włączanie usługi Event Hubs Capture za pomocą witryny Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Włączanie przechwytywania Event Hubs przy użyciu szablonu Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www.apache.org/dist/avro/stable/java/avro-tools-1.9.2.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Przesyłanie strumieniowe na dużą skalę: Przechwytywanie Event Hubs]: https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
