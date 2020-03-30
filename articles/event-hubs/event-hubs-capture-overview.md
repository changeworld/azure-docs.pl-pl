---
title: Przechwytywanie zdarzeń przesyłania strumieniowego — usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie funkcji przechwytywania, która umożliwia przechwytywanie zdarzeń przesyłania strumieniowego za pośrednictwem usługi Azure Event Hubs.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265014"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Przechwytywanie zdarzeń za pośrednictwem usługi Azure Event Hubs w usłudze Azure Blob Storage lub usłudze Azure Data Lake Storage
Usługa Azure Event Hubs umożliwia automatyczne przechwytywanie danych przesyłania strumieniowego w centrach zdarzeń w [wybranym koncie usługi Azure Data](https://azure.microsoft.com/services/storage/blobs/) [Storage Gen 1 lub Gen 2,](https://azure.microsoft.com/services/data-lake-store/) z dodatkową elastycznością określania przedziału czasu lub rozmiaru. Konfiguracja przechwytywania jest szybka, nie ma żadnych kosztów administracyjnych, aby go uruchomić i skaluje się automatycznie za pomocą [jednostek przepływności](event-hubs-scalability.md#throughput-units)Centrum zdarzeń . Przechwytywanie usługi Event Hubs to najprostszy sposób ładowania danych przesyłanych strumieniowo na platformę Azure i umożliwia skupienie się na przetwarzaniu danych, a nie na przechwytywaniu danych.

Przechwytywanie centrów zdarzeń umożliwia przetwarzanie potoków opartych w czasie rzeczywistym i wsadowych w tym samym strumieniu. Oznacza to, że możesz tworzyć rozwiązania, które rosną wraz z twoimi potrzebami w czasie. Niezależnie od tego, czy dziś tworzysz systemy wsadowe z myślą o przyszłym przetwarzaniu w czasie rzeczywistym, czy chcesz dodać wydajną ścieżkę na zimno do istniejącego rozwiązania w czasie rzeczywistym, usługa Event Hubs Capture ułatwia pracę z przesyłaniem strumieniowym danych.


## <a name="how-event-hubs-capture-works"></a>Jak działa przechwytywanie centrów zdarzeń

Centra zdarzeń to trwały bufor przechowywania czasu dla transferu danych przychodzących telemetrycznych, podobny do dziennika rozproszonego. Kluczem do skalowania w Centrach zdarzeń jest podzielony na [partycje model konsumenta.](event-hubs-scalability.md#partitions) Każda partycja jest niezależnym segmentem danych i jest zużywana niezależnie. Z biegiem czasu te dane są wyłączone na podstawie konfigurowalnyego okresu przechowywania. W rezultacie danego centrum zdarzeń nigdy nie zostanie "zbyt pełne".

Przechwytywanie usług Event Hubs umożliwia określenie własnego konta i kontenera magazynu obiektów Blob platformy Azure lub konta usługi Azure Data Lake Storage, które są używane do przechowywania przechwyconych danych. Te konta mogą znajdować się w tym samym regionie co centrum zdarzeń lub w innym regionie, co zwiększa elastyczność funkcji Przechwytywanie centrów zdarzeń.

Przechwycone dane są zapisywane w formacie [Apache Avro:][Apache Avro] kompaktowy, szybki, binarny format, który zapewnia bogate struktury danych ze schematem wbudowanym. Ten format jest szeroko stosowany w ekosystemie Hadoop, usługi Stream Analytics i fabryce danych platformy Azure. Więcej informacji na temat pracy z avro można znaleźć w dalszej części tego artykułu.

### <a name="capture-windowing"></a>Przechwytywanie okien

Przechwytywanie centrów zdarzeń umożliwia skonfigurowanie okna do sterowania przechwytywaniem. To okno jest minimalny rozmiar i czas konfiguracji z "pierwszy wygrywa zasady", co oznacza, że pierwszy wyzwalacz napotkał powoduje operację przechwytywania. Jeśli masz 15-minutowe okno przechwytywania 100 MB i wysyłasz 1 MB na sekundę, okno rozmiaru wyzwala się przed przedziałem czasu. Każda partycja przechwytuje niezależnie i zapisuje ukończony blok blob w momencie przechwytywania, nazwany dla czasu, w którym napotkano interwał przechwytywania. Konwencja nazewnictwa magazynu jest następująca:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Należy zauważyć, że wartości dat są wyściełane zerami; przykładowa nazwa pliku może być:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

W przypadku, gdy obiekt blob magazynu platformy Azure jest tymczasowo niedostępny, usługa Capture usługi Event Hubs zachowa dane dla okresu przechowywania danych skonfigurowanego w centrum zdarzeń i z powrotem wypełni dane po ponownym udostępnieniu konta magazynu.

### <a name="scaling-to-throughput-units"></a>Skalowanie do jednostek przepływności

Ruch w centrach zdarzeń jest kontrolowany przez [jednostki przepływności.](event-hubs-scalability.md#throughput-units) Pojedyncza jednostka przepływności umożliwia 1 MB na sekundę lub 1000 zdarzeń na sekundę ruchu przychodzącego i dwa razy tyle ruchu wychodzącego. Standardowe centra zdarzeń można skonfigurować za pomocą jednostek przepływności 1-20, a więcej można kupić za [pomocą żądania wsparcia][support request]zwiększenia przydziału . Użycie wykraczające poza zakupione jednostki przepływności jest ograniczane. Usługa Event Hubs Capture kopiuje dane bezpośrednio z wewnętrznego magazynu centrum zdarzeń, omijając przydziały ruchu wychodzącego jednostki przepływności i zapisując swój ruch wychodzący dla innych czytników przetwarzania, takich jak Usługa Stream Analytics lub Spark.

Po skonfigurowaniu usługa Event Hubs Capture jest uruchamiana automatycznie po wysłaniu pierwszego zdarzenia i jest kontynuowana. Aby ułatwić przetwarzanie podrzędne wiedzieć, że proces działa, Centrum zdarzeń zapisuje puste pliki, gdy nie ma żadnych danych. Ten proces zapewnia przewidywalną kadencję i znacznik, który może zasilać procesory wsadowe.

## <a name="setting-up-event-hubs-capture"></a>Konfigurowanie przechwytywania centrów zdarzeń

Przechwytywanie można skonfigurować w czasie tworzenia centrum zdarzeń przy użyciu [witryny Azure portal](https://portal.azure.com)lub przy użyciu szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Włączanie funkcji przechwytywania usługi Event Hubs przy użyciu witryny Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Tworzenie przestrzeni nazw usługi Event Hubs z centrum zdarzeń i włączanie funkcji przechwytywania przy użyciu szablonu usługi Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Eksplorowanie przechwyconych plików i praca z avro

Event Hubs Capture tworzy pliki w formacie Avro, jak określono w skonfigurowanym przedziale czasu. Można wyświetlić te pliki w dowolnym narzędziu, takim jak [Azure Storage Explorer][Azure Storage Explorer]. Możesz pobrać pliki lokalnie, aby nad nimi pracować.

Pliki tworzone przez Event Hubs Capture mają następujący schemat Avro:

![Schemat Avro][3]

Łatwym sposobem na eksplorowanie plików Avro jest użycie słoika [Avro Tools][Avro Tools] firmy Apache. Można również użyć [Apache Drill][Apache Drill] dla lekkiego środowiska opartego na języku SQL lub [Apache Spark][Apache Spark] do wykonywania złożonych przetwarzania rozproszonego na pochłoniętych danych. 

### <a name="use-apache-drill"></a>Użyj Apache Drill

[Apache Drill][Apache Drill] to "aparat zapytań SQL typu open source do eksploracji dużych zbiorów danych", który może wysyłać zapytania do danych strukturalnych i częściowo ustrukturyzowanych, gdziekolwiek się znajduje. Aparat może działać jako węzeł autonomiczny lub jako ogromny klaster dla dużej wydajności.

Dostępna jest natywna obsługa magazynu obiektów Blob platformy Azure, co ułatwia wykonywanie zapytań o dane w pliku Avro, zgodnie z opisem w dokumentacji:

[Apache Drill: Wtyczka usługi Azure Blob Storage][Apache Drill: Azure Blob Storage Plugin]

Aby łatwo zbadać przechwycone pliki, można utworzyć i wykonać maszynę wirtualną z Apache Drill włączone za pośrednictwem kontenera, aby uzyskać dostęp do magazynu obiektów blob platformy Azure:

https://github.com/yorek/apache-drill-azure-blob

Pełna próbka end-to-end jest dostępna w repozytorium Przesyłania strumieniowego w skali:

[Przesyłanie strumieniowe w skali: przechwytywanie centrów zdarzeń]

### <a name="use-apache-spark"></a>Użyj Apache Spark

[Apache Spark][Apache Spark] to "zunifikowany aparat analityczny do przetwarzania danych na dużą skalę". Obsługuje różne języki, w tym SQL, i można łatwo uzyskać dostęp do magazynu obiektów Blob platformy Azure. Istnieje kilka opcji uruchamiania platformy Apache Spark na platformie Azure, a każda z nich zapewnia łatwy dostęp do magazynu obiektów Blob platformy Azure:

- [HDInsight: Adresowanie plików w magazynie platformy Azure][HDInsight: Address files in Azure storage]
- [Usługi Azure Databricks: magazyn obiektów blob platformy Azure][Azure Databricks: Azure Blob Storage]
- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/spark-job) 

### <a name="use-avro-tools"></a>Korzystanie z narzędzi Avro

[Avro Tools][Avro Tools] są dostępne w pakiecie słoika. Po pobraniu pliku jar, można zobaczyć schemat określonego pliku Avro, uruchamiając następujące polecenie:

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

Za pomocą Avro Tools można również przekonwertować plik na format JSON i wykonać inne przetwarzanie.

Aby wykonać bardziej zaawansowane przetwarzanie, pobierz i zainstaluj Avro dla wyboru platformy. W momencie pisania tego tekstu dostępne są implementacje dla\#C, C++, C, Java, NodeJS, Perl, PHP, Python i Ruby.

Apache Avro ma kompletne przewodniki Wprowadzenie dla [Java][Java] i [Python][Python]. Możesz również przeczytać artykuł [Wprowadzenie do przechwytywania centrów zdarzeń.](event-hubs-capture-python.md)

## <a name="how-event-hubs-capture-is-charged"></a>Jak jest naliczana opłata za przechwytywanie centrów zdarzeń

Przechwytywanie centrów zdarzeń jest mierzone podobnie jak jednostki przepływności: jako opłata godzinowa. Opłata jest wprost proporcjonalna do liczby jednostek przepływności zakupionych dla obszaru nazw. Wraz ze wzrostem i zmniejszeniem jednostek przepływności liczniki przechwytywania centrów zdarzeń zwiększają się i maleją, aby zapewnić pasującą wydajność. Liczniki występują w tandemie. Aby uzyskać szczegółowe informacje o cenach, zobacz [Ceny centrów zdarzeń](https://azure.microsoft.com/pricing/details/event-hubs/). 

Należy zauważyć, że przechwytywanie nie zużywa przydziału ruchu wychodzącego, ponieważ jest rozliczany oddzielnie. 

## <a name="integration-with-event-grid"></a>Integracja z siatką zdarzeń 

Subskrypcję usługi Azure Event Grid można utworzyć z obszarem nazw centrum zdarzeń jako jego źródłem. W poniższym samouczku pokazano, jak utworzyć subskrypcję usługi Event Grid z Centrum zdarzeń jako źródłem i aplikacją usługi Azure Functions jako [ujście: Przetwarzaj i migruj przechwycone dane centrum zdarzeń do magazynu danych SQL przy użyciu siatki zdarzeń i funkcji platformy Azure.](store-captured-data-data-warehouse.md)

## <a name="next-steps"></a>Następne kroki
Przechwytywanie centrów zdarzeń to najprostszy sposób na uzyskanie danych na platformie Azure. Korzystając z usługi Azure Data Lake, Usługi Azure Data Factory i usługi Azure HDInsight, można wykonywać przetwarzanie wsadowe i inne analizy przy użyciu znanych narzędzi i platform do wyboru, w dowolnej skali, w dowolnej skali.

Dowiedz się, jak włączyć tę funkcję przy użyciu witryny Azure portal i szablonu usługi Azure Resource Manager:

- [Włączanie usługi Event Hubs Capture za pomocą witryny Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Włącz przechwytywanie centrów zdarzeń za pomocą szablonu usługi Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


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
[Przesyłanie strumieniowe w skali: przechwytywanie centrów zdarzeń]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
