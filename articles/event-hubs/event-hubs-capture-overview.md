---
title: Omówienie usługi Azure Event hubs przechwytywania | Dokumentacja firmy Microsoft
description: Przechwytywania danych telemetrycznych z przechwytywania centra zdarzeń
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: 00eee302cc15d94ec62f5f3332e18ee2df24f5cd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311437"
---
# <a name="azure-event-hubs-capture"></a>Usługi Azure Event Hubs przechwytywania

Azure przechwytywania centra zdarzeń umożliwia automatycznie dostarczyć dane przesyłane strumieniowo z usługi Event Hubs do [magazynu obiektów Blob Azure](https://azure.microsoft.com/services/storage/blobs/) lub [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) konta wybranych wzbogaconych określania przedział czasu lub rozmiarze. Konfigurowanie przechwytywania jest szybkie, są koszty administracyjne do jego działania i automatycznie skaluje się z usługą Event Hubs [jednostek przepływności](event-hubs-features.md#capacity). Przechwytywanie centra zdarzeń jest najprostszym sposobem, aby załadować dane przesyłane strumieniowo do platformy Azure i umożliwia fokus przetwarzania danych, a nie do przechwytywania danych.

Przechwyć centra zdarzeń umożliwia przetwarzanie potoków w czasie rzeczywistym i na podstawie partii dla tego samego strumienia. Oznacza to, że można tworzyć rozwiązania, które zwiększa potrzebami wraz z upływem czasu. Czy kompilujesz systemów opartych na procesorze partii z oka kierunku przyszłego przetwarzania w czasie rzeczywistym lub chcesz dodać wydajne zimnych ścieżkę do istniejącego rozwiązania w czasie rzeczywistym, przechwytywania centra zdarzeń ułatwia pracę z strumienia danych.

## <a name="how-event-hubs-capture-works"></a>Jak działa przechwytywania centra zdarzeń

Usługa Event Hubs to czas przechowywania trwałe buforu dla wejściowych telemetrii, podobnie jak dziennika rozproszonej. Kluczem do skalowania w usłudze Event Hubs jest [partycjonowanego modelu odbiorców](event-hubs-features.md#partitions). Każda partycja jest niezależna segmentu danych i jest używane niezależnie. Wraz z upływem czasu te dane wieku, na podstawie okresu przechowywania można skonfigurować. W związku z tym Centrum zdarzeń danego nigdy nie pobiera "zapełniony."

Przechwyć centra zdarzeń można określić własne konta magazynu obiektów Blob platformy Azure i kontener lub konta usługi Azure Data Lake Store, które są używane do przechowywania przechwyconych danych. Te konta można w tym samym regionie co Centrum zdarzeń lub w innym regionie, dodanie do elastyczność funkcji przechwytywania centrów zdarzeń.

Przechwycone dane są zapisywane [Apache Avro] [ Apache Avro] format: compact, szybkie i binarnego formatu, który zawiera struktury danych sformatowanego z wbudowanego schematu. Ten format jest powszechnie używany w ekosystemie Hadoop, Stream Analytics i fabryki danych Azure. Więcej informacji na temat pracy z Avro jest dostępna w dalszej części tego artykułu.

### <a name="capture-windowing"></a>Przechwyć okien

Przechwyć centra zdarzeń umożliwia konfigurowanie okna do kontrolowania przechwytywania. To okno jest minimalny rozmiar i Konfiguracja czasu za pomocą "pierwszy wins zasad," oznacza, że to pierwszy wyzwalacz napotkano powoduje operacji przechwytywania. Jeśli masz 15 minutowych, 100 MB okna Przechwytywanie i wysłać 1 MB na sekundę, wyzwalacze rozmiar okna przed przedział czasu. Każdej partycji przechwytuje niezależnie i zapisuje ukończone blokowego obiektu blob w czasie przechwytywania, o nazwie raz podczas interwału przechwytywania. Konwencja nazewnictwa magazynu jest następujący:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Należy pamiętać, że wartości daty są dopełniane zerami; przykład nazwy pliku może być:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

### <a name="scaling-to-throughput-units"></a>Skalowanie do jednostki przepływności

Ruch centra zdarzeń jest kontrolowany przez [jednostek przepływności](event-hubs-features.md#capacity). Pojedyncza jednostka przepływności umożliwia 1 MB na sekundę lub 1000 zdarzeń na sekundę przychodzące i dwa razy ilość wyjście. Standardowa usługa Event Hubs można skonfigurować za pomocą 1-20 jednostek przepływności i możesz kupić więcej zwiększyć limit przydziału [żądania obsługi][support request]. Użycie poza jednostek przepływności zakupionych jest ograniczony. Przechwyć centra zdarzeń kopiuje dane bezpośrednio w pamięci wewnętrznej usługi Event Hubs, pomijanie przydziały wyjście jednostki przepływności i zapisywanie wyjście z innych czytniki przetwarzania, takich jak analiza strumienia lub Spark.

Po skonfigurowaniu przechwytywania centra zdarzeń jest uruchamiane automatycznie po wysłaniu pierwszego wydarzenia i będzie kontynuował działanie. Aby ułatwić podrzędne przetwarzania wiedzieć, czy proces działa usługi Event Hubs zapisuje pustych plików, czy nie ma żadnych danych. Ten proces zapewnia przewidywalną okresach i znaczników, który może dostarczyć procesorów partii.

## <a name="setting-up-event-hubs-capture"></a>Konfigurowanie przechwytywania centra zdarzeń

Przechwytywania można skonfigurować przy użyciu czasu tworzenia Centrum zdarzeń [portalu Azure](https://portal.azure.com), lub za pomocą szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Włącz przechwytywanie centra zdarzeń przy użyciu portalu Azure](event-hubs-capture-enable-through-portal.md)
- [Tworzenie przestrzeni nazw usługi Event Hubs z Centrum zdarzeń i Włączanie rejestracji przy użyciu szablonu usługi Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

## <a name="exploring-the-captured-files-and-working-with-avro"></a>Eksplorowanie przechwyconych plików i Praca z Avro

Przechwyć centra zdarzeń tworzy pliki w formacie Avro, jak określono w oknie skonfigurowanym czasie. Pliki te można wyświetlać takie jak dowolnego narzędzia [Eksploratora usługi Storage Azure][Azure Storage Explorer]. Możesz pobrać plików lokalnie, aby pracować z nimi.

Pliki tworzone przez funkcję przechwytywania centra zdarzeń mają następujące schemacie Avro:

![][3]

Łatwo eksplorować plików Avro polega na użyciu [narzędzia Avro] [ Avro Tools] jar z Apache. Po pobraniu tego jar, można wyświetlić schemat określonego pliku Avro, uruchamiając następujące polecenie:

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

Umożliwia także Avro narzędzia do konwersji do formatu JSON pliku i wykonywania innych procesów.

Aby przeprowadzić bardziej zaawansowanych przetwarzania, należy pobrać i zainstalować Avro dla wybór platformy. W momencie pisania tego dokumentu, są dostępne dla C, C++, C implementacje\#, Java, NodeJS, Perl, PHP, Python i Ruby.

Apache Avro ma pełne wprowadzenie przewodniki dotyczące [Java] [ Java] i [Python][Python]. Można również znaleźć [wprowadzenie do przechwytywania centra zdarzeń](event-hubs-capture-python.md) artykułu.

## <a name="how-event-hubs-capture-is-charged"></a>Jak jest rozliczana przechwytywania centra zdarzeń

Przechwyć centra zdarzeń taryfowych podobnie do jednostek przepływności: jako dodatkowy co godzinę. Opłata jest wprost proporcjonalny do liczby jednostek przepływności zakupionych dla przestrzeni nazw. Jednostki przepływności są zwiększyć i zmniejszyć, przechwytywania centra zdarzeń liczników zwiększyć i zmniejszyć zapewnienie wydajności dopasowania. Liczniki wystąpić w połączeniu. Aby uzyskać szczegółowe informacje o cenach, zobacz [cennik usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/). 

## <a name="next-steps"></a>Kolejne kroki

Przechwyć centra zdarzeń jest najprostszym sposobem pobrania danych na platformie Azure. Za pomocą usługi Azure Data Lake, fabryki danych Azure i usłudze Azure HDInsight, można wykonać przetwarzania wsadowego i innych analytics przy użyciu znanych narzędzi i platform wybrane, w dowolnej skali należy.

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Rozpoczynanie pracy wysyłanie i odbieranie zdarzeń](event-hubs-dotnet-framework-getstarted-send.md)
* [Przegląd usługi Event Hubs][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.2/java/avro-tools-1.8.2.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
