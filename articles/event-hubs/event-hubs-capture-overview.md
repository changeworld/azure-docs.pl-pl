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
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: cc74bfe9bf9e5f33b7cf05ebb19b44ab8b3bea43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822433"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Przechwytywanie zdarzeń za pomocą usługi Azure Event Hubs w usłudze Azure Blob Storage lub magazynu usługi Azure Data Lake
Usługa Azure Event Hubs umożliwia automatyczne przechwytywanie danych przesyłanych strumieniowo, w centrach zdarzeń w [usługi Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/) lub [usługi Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/) wybrane konto usługi, przy użyciu dodanych elastycznych określenie interwału czasu lub rozmiaru. Konfigurowanie Capture jest szybkie, istnieją żadnych dodatkowych kosztów administracyjnych, aby go uruchomić i automatycznie skaluje się z usługą Event Hubs [jednostek przepływności](event-hubs-features.md#throughput-units). Funkcja przechwytywania usługi Event Hubs jest najprostszym sposobem ładowania danych strumieniowych na platformę Azure i pozwala skoncentrować się na przetwarzaniu danych zamiast na przechwytywania danych.

Funkcja przechwytywania usługi Event Hubs pozwala przetwarzać potoki w czasie rzeczywistym i opartych na partiach, w tym samym strumieniu. Oznacza to, że możesz tworzyć rozwiązania, które wzrostu z Twoimi potrzebami wraz z upływem czasu. Czy tworzysz systemów opartych na procesorze batch już dzisiaj za pomocą śledzenia do przyszłego przetwarzania w czasie rzeczywistym, które chcesz dodać wydajne ścieżki nieaktywnej do istniejącego rozwiązania w czasie rzeczywistym, funkcja przechwytywania usługi Event Hubs sprawia, że praca łatwiejsze danych przesyłanych strumieniowo.

> [!NOTE]
> Obecnie funkcja przechwytywania usługi Event Hubs obsługuje tylko Gen 1 z usługi Azure Data Lake Store, nie Gen 2. 

## <a name="how-event-hubs-capture-works"></a>Jak działa funkcja przechwytywania usługi Event Hubs

Usługa Event Hubs jest trwały bufor czasu przechowywania dla przychodzących danych telemetrycznych podobne do rozproszonej dziennika. Kluczem do skalowania w usłudze Event Hubs jest [partycjonowanego modelu odbiorców](event-hubs-features.md#partitions). Każda partycja jest niezależna segmentu danych i jest używane niezależnie. Wraz z upływem czasu te dane wieku, na podstawie okresu przechowywania można skonfigurować. W wyniku podanego zdarzenia koncentratora nigdy nie pobiera "zbyt pełnej."

Funkcja przechwytywania usługi Event Hubs można określić własnego konta usługi Azure Blob storage i kontenera lub konta usługi Azure Data Lake Store, które są używane do przechowywania przechwyconych danych. Te konta można w tym samym regionie, co Centrum zdarzeń lub w innym regionie znajdującym się dodanie do elastyczność funkcji przechwytywania usługi Event Hubs.

Przechwycone dane są zapisywane [Apache Avro] [ Apache Avro] format: formatu compact, szybkie i binarny, który zapewnia struktury zaawansowanych danych za pomocą wbudowanego schematu. Ten format jest powszechnie używany w ekosystemie usługi Hadoop, usługi Stream Analytics i Azure Data Factory. Więcej informacji na temat pracy z Avro jest dostępna w dalszej części tego artykułu.

### <a name="capture-windowing"></a>Przechwytywanie obsługi okien

Funkcja przechwytywania usługi Event Hubs umożliwia konfigurowanie przedziału do kontrolowania przechwytywania. To okno jest minimalny rozmiar i Konfiguracja czasu za pomocą "pierwszy wins zasad," oznacza, że to pierwszy wyzwalacz, napotkano powoduje, że operacji przechwytywania. W przypadku 15 minutowy 100 MB okna Przechwytywanie i wysłać 1 MB na sekundę, wyzwalacze okna rozmiar przed w danym przedziale czasu. Każda partycja przechwytuje niezależnie i zapisuje w czasie przechwytywania, ukończone blokowego obiektu blob o nazwie czas napotkano interwał przechwytywania. Konwencja nazewnictwa storage jest następująca:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Należy pamiętać, że wartości daty są dopełniane zerami; przykład nazwy pliku może być:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

### <a name="scaling-to-throughput-units"></a>Skalowanie do wszystkich jednostek przepływności

Ruch do centrów zdarzeń jest kontrolowane przez [jednostek przepływności](event-hubs-features.md#throughput-units). Pojedyncza jednostka przepływności umożliwia 1 MB na sekundę lub 1000 zdarzeń na sekundę transferu danych przychodzących i dwa razy ilość danych wychodzących. Standardowa usługi Event Hubs może być skonfigurowany z 1-20 jednostek przepływności i nabyć więcej jednostek z limitem przydziału o zwiększenie [żądania pomocy technicznej][support request]. Użycie jednostek przepływności zakupionych jest ograniczane. Funkcja przechwytywania usługi Event Hubs kopiuje dane bezpośrednio z pamięci wewnętrznej usługi Event Hubs, pomijanie limity przydziału ruchu wychodzącego jednostek przepływności i zapisywanie swojej ruchu wychodzącego dla innych czytników przetwarzania, takich jak Stream Analytics lub Spark.

Po skonfigurowaniu usługi Event Hubs Capture jest uruchamiany automatycznie po wysłaniu pierwszego zdarzenia i będzie kontynuował działanie. Aby ułatwić Twojej przetwarzania transmisji dla klientów dowiedzieć się, że proces działa usługa Event Hubs zapisuje pustych plików, czy nie ma żadnych danych. Ten proces obejmuje tempa przewidywalny i znaczników, który może przekazywać procesorów usługi batch.

## <a name="setting-up-event-hubs-capture"></a>Konfigurowanie przechwytywania usługi Event Hubs

Przechwytywanie można skonfigurować w Centrum zdarzeń tworzenia czasu za pomocą [witryny Azure portal](https://portal.azure.com), lub za pomocą szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Włączanie przechwytywania usługi Event Hubs przy użyciu witryny Azure portal](event-hubs-capture-enable-through-portal.md)
- [Tworzenie przestrzeni nazw usługi Event Hubs z Centrum zdarzeń i włączanie funkcji przechwytywania przy użyciu szablonu usługi Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Eksplorowanie plików przechwyconej i pracą z nimi przy użyciu systemu Avro

Funkcja przechwytywania usługi Event Hubs tworzy pliki w formacie Avro, zgodnie z instrukcjami na przedział czasu skonfigurowane. Można wyświetlić tych plików w dowolne narzędzie takie jak [Eksploratora usługi Azure Storage][Azure Storage Explorer]. Możesz pobrać pliki lokalnie w celu pracy z nimi.

Pliki tworzone przez przechwytywania usługi Event Hubs mają następujące schemacie Avro:

![Schemacie Avro][3]

Łatwe Eksplorowanie plików Avro polega na użyciu [narzędzia Avro] [ Avro Tools] jar z Apache. Można również użyć [Apache Drill] [ Apache Drill] dla lekkie środowisko oparte na SQL lub [platformy Apache Spark] [ Apache Spark] do wykonywania złożonych rozproszonych Przetwarzanie pozyskiwanych danych. 

### <a name="use-apache-drill"></a>Use Apache Drill

[Apache Drill] [ Apache Drill] to "open source SQL zapytań aparat do eksploracji danych big data" który wykonywać zapytania na danych z częściową strukturą i ze strukturą, wszędzie tam, gdzie jest. Aparat można uruchomić jako węzeł autonomiczny lub jako klaster ogromna uzyskać wysoką wydajność.

Macierzystą obsługę w usłudze Azure Blob storage jest dostępna, która ułatwia zapytania o dane w pliku Avro zgodnie z opisem w dokumentacji:

[Apache Drill: Azure Blob Storage Plugin][Apache Drill: Azure Blob Storage Plugin]

Łatwo kwerendy przechwyconych plików, można tworzyć i wykonać Maszynę wirtualną przy użyciu Apache Drill włączyć za pomocą kontenera dostępu do magazynu obiektów Blob platformy Azure:

https://github.com/yorek/apache-drill-azure-blob

Przykładowe pełnej end-to-end jest dostępna w przesyłania strumieniowego w repozytorium skalowania:

[Przesyłanie strumieniowe w dużej skali: Funkcja przechwytywania usługi Event Hubs]

### <a name="use-apache-spark"></a>Use Apache Spark

[Platforma Apache Spark] [ Apache Spark] "ujednolicone analytics to aparat obsługi jest do przetwarzania danych na dużą skalę." Ona obsługę innych języków, w tym SQL i mogą łatwo uzyskiwać dostęp do usługi Azure Blob storage. Dostępne są dwie opcje uruchamiania platformy Apache Spark na platformie Azure, a jednocześnie zapewnia łatwy dostęp do usługi Azure Blob storage:

- [HDInsight: Adresowanie plików w usłudze Azure storage][HDInsight: Address files in Azure storage]
- [Usługa Azure Databricks: Usługa Azure Blob storage][Azure Databricks: Azure Blob Storage]

### <a name="use-avro-tools"></a>Korzystaj z narzędzi Avro

[Narzędzia Avro] [ Avro Tools] są dostępne jako pakiet jar. Po pobraniu pliku jar, można wyświetlić schemat określonego pliku Avro, uruchamiając następujące polecenie:

```shell
java -jar avro-tools-1.8.2.jar getschema <name of capture file>
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

Aby przeprowadzić bardziej zaawansowane przetwarzanie, Pobierz i zainstaluj Avro z dowolnie wybranych platform. W momencie pisania tego dokumentu, istnieją implementacje dla języka C, C++, C\#, Java, NodeJS, Perl, PHP, Python i Ruby.

Apache Avro ma pełne przewodniki wprowadzenie dla [Java] [ Java] i [Python][Python]. Można również przeczytać [rozpoczęcie korzystania z usługi Event Hubs Capture](event-hubs-capture-python.md) artykułu.

## <a name="how-event-hubs-capture-is-charged"></a>Jak opłata przechwytywania usługi Event Hubs

Funkcja przechwytywania usługi Event Hubs jest mierzone w podobny sposób do wszystkich jednostek przepływności: jako dodatkowy co godzinę. Opłata jest wprost proporcjonalna do liczby jednostek przepływności zakupionych dla przestrzeni nazw. Jednostki przepływności są zwiększyć i zmniejszyć, liczniki przechwytywania usługi Event Hubs zwiększyć i zmniejszyć do zapewnienia wydajności dopasowania. Liczniki wystąpić w połączeniu sekcją. Aby uzyskać szczegółowe informacje o cenach, zobacz [cen usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/). 

## <a name="integration-with-event-grid"></a>Integracja z usługą Event Grid 

Możesz utworzyć subskrypcję usługi Azure Event Grid z przestrzeni nazw usługi Event Hubs jako źródła. Następującego samouczka dowiesz się, jak utworzyć subskrypcję usługi Event Grid przy użyciu Centrum zdarzeń jako źródło i aplikację usługi Azure Functions jako ujścia: [Przetwarzania, a następnie przeprowadzić migrację przechwycone dane z usługi Event Hubs w usłudze SQL Data Warehouse przy użyciu usługi Event Grid i Azure Functions](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Kolejne kroki

Funkcja przechwytywania usługi Event Hubs jest najprostszym sposobem pobierania danych do platformy Azure. Za pomocą usługi Azure Data Lake, Azure Data Factory i Azure HDInsight, można wykonać przetwarzania wsadowego i innych analiz, przy użyciu dobrze znanych narzędzi i platform wybrane w dowolnej skali konieczne.

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Wprowadzenie do wysyłania oraz odbierania zdarzeń](event-hubs-dotnet-framework-getstarted-send.md)
* [Przegląd usługi Event Hubs][Event Hubs overview]

[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www-us.apache.org/dist/avro/avro-1.8.2/java/avro-tools-1.8.2.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage#address-files-in-azure-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Przesyłanie strumieniowe w dużej skali: Funkcja przechwytywania usługi Event Hubs]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
