---
title: 'Połączenie danych: dane wejściowe ze strumienia zdarzeń strumienia danych | Dokumentacja firmy Microsoft'
description: Więcej informacji na temat konfigurowania połączenia danych do usługi Stream Analytics o nazwie "dane wejściowe". Dane wejściowe zawierają strumień danych zdarzeń i również danych referencyjnych.
keywords: strumień danych, połączenie danych strumienia zdarzeń
services: stream-analytics
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 8155823c-9dd8-4a6b-8393-34452d299b68
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/11/2017
ms.author: sngun
ms.openlocfilehash: 0ddc3187e9fc0664838dd07f781f4d7e2e4a7fe0
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>Połączenie danych: Dowiedz się więcej o danych strumienia danych wejściowych ze zdarzeń w celu usługi analiza strumienia
Połączenie danych do zadania usługi Stream Analytics jest strumienia zdarzeń źródła danych, które są określone jako zadanie *wejściowych*. Analiza strumienia ma najwyższej jakości integracji z źródeł strumienia danych Azure, w tym [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [Centrum IoT Azure](https://azure.microsoft.com/services/iot-hub/), i [magazynu obiektów Blob Azure](https://azure.microsoft.com/services/storage/blobs/). Tych źródeł danych wejściowych można z tej samej subskrypcji platformy Azure jako zadanie analizy lub innej subskrypcji.

## <a name="data-input-types-data-stream-and-reference-data"></a>Dane wejściowe typy: danych strumienia i danych referencyjnych
Jak przekazania danych do źródła danych, ma używane przez zadanie usługi analiza strumienia i przetwarzane w czasie rzeczywistym. Dane wejściowe są podzielone na dwa typy: dane strumienia danych wejściowych i odwołują się dane wejściowe dane.

### <a name="data-stream-inputs"></a>Dane wejściowe strumienia danych
Strumień danych jest niepowiązany sekwencji zdarzeń w czasie. Zadania usługi analiza strumienia musi zawierać co najmniej jednego danych elementu wejściowego strumienia. Centra zdarzeń, Centrum IoT i magazynu obiektów Blob są obsługiwane jako źródeł dla wejścia strumienia danych. Centra zdarzeń są używane do zbierania strumieni zdarzeń z wielu urządzeń i usług. Strumienie te mogą obejmować źródła działań mediów społecznościowych, handlu standardowych informacji lub dane z czujników. Centra IoT są optymalizowane w celu zbierania danych z połączonych urządzeń w scenariuszach Internetu rzeczy (IoT).  Magazyn obiektów blob może służyć jako źródło danych wejściowych do wprowadzania danych zbiorczego jako strumienia, takich jak pliki dziennika.  

### <a name="reference-data"></a>Dane referencyjne
Analiza strumienia obsługuje również znane jako dane wejściowe *danych referencyjnych*. Jest to dane pomocnicze, który jest statycznych lub który zmienia się powoli. Zazwyczaj jest używany do wykonywania korelacji i wyszukiwania. Na przykład można sprzęgnąć danych w danych wejściowych strumienia z danymi w danych referencyjnych, tak jak będzie wykonywać sprzężenia SQL do odszukania wartości statyczne. Magazyn obiektów Blob Azure jest obecnie obsługiwane tylko źródła danych wejściowych danych referencyjnych. Odwołanie do źródła danych typu blob są ograniczone do 100 MB rozmiar.

Aby dowiedzieć się, jak utworzyć odwołanie do danych wejściowych danych, zobacz [danych referencyjnych użyj](stream-analytics-use-reference-data.md).  

## <a name="compression"></a>Kompresja

Usługa Azure Stream Analytics obsługuje kompresję we wszystkich strumienia wejściowego źródeł danych (magazyn centra zdarzeń, Centrum IoT i obiektów Blob). Ta funkcja dodaje nową opcję listy rozwijanej **wprowadzania nowych** bloku w portalu Azure, co umożliwia opcjonalnie do skompresowania strumienie danych. Obecnie obsługiwane są typy odwołanie - Brak GZip i kompresję Deflate. Obsługa kompresji nie jest dostępna dla danych referencyjnych.

Nie trzeba określać typ kompresji serializacji Avro. Jeśli dane wejściowe Avro jest skompresowany, jest obsługiwane przezroczysty. 

## <a name="create-data-stream-input-from-event-hubs"></a>Tworzenie elementu wejściowego strumienia danych z usługi Event Hubs

Usługa Azure Event Hubs zapewnia wysoką skalowalnością ingestors zdarzeń publikowania / subskrypcji. Centrum zdarzeń można zbierać miliony zdarzeń na sekundę, dzięki czemu możliwe jest przetwarzanie i analizowanie olbrzymich ilości danych wytworzonych przez podłączone urządzenia i aplikacje. Centra zdarzeń i analiza strumienia razem umożliwiają end-to-end rozwiązania do analiz w czasie rzeczywistym — usługi Event Hubs umożliwia strumieniowe źródło zdarzeń na platformie Azure w czasie rzeczywistym, i zadania usługi analiza strumienia może przetwarzać tych zdarzeń w czasie rzeczywistym. Na przykład możesz wysłać kliknięć w sieci web, odczyty czujników lub online dziennika zdarzeń do usługi Event Hubs. Następnie można utworzyć zadania usługi analiza strumienia do użycia usługi Event Hubs jako strumieni danych wejściowych w czasie rzeczywistym filtrowanie, agregację i korelacji.

Sygnatura czasowa domyślne zdarzenia pochodzące z usługi Event Hubs w Stream Analytics jest sygnatury czasowej, które zdarzenia w przypadku odebrania koncentratora, który jest `EventEnqueuedUtcTime`. Do przetwarzania danych jako strumień przy użyciu sygnatury czasowej w przypadku ładunku, należy użyć [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) — słowo kluczowe.

### <a name="consumer-groups"></a>Grupy odbiorców
Należy skonfigurować każdego Centrum zdarzeń usługi analiza strumienia danych wejściowych ma własną grupę odbiorców. Jeśli zadanie zawiera samosprzężenie lub składa się z wielu danych wejściowych, niektóre dane wejściowe mogą odczytać przez więcej niż jeden czytnik poniżej. Taka sytuacja wpływa na liczbę czytników w grupie jednego konsumenta. Aby zapobiec przekroczeniu limitu centra zdarzeń w pięciu czytników dla każdej grupy odbiorców dla każdej partycji, jest najlepszym rozwiązaniem, aby wyznaczyć grupy odbiorców dla każdego zadania usługi analiza strumienia. Istnieje również limit 20 grup odbiorców, na Centrum zdarzeń. Aby uzyskać więcej informacji, zobacz [Event Hubs Programming Guide](../event-hubs/event-hubs-programming-guide.md).

### <a name="configure-an-event-hub-as-a-data-stream-input"></a>Konfigurowanie Centrum zdarzeń jako strumienia danych wejściowych
W poniższej tabeli opisano każdej właściwości w **wprowadzania nowych** bloku w portalu Azure podczas konfigurowania Centrum zdarzeń jako dane wejściowe.

| Właściwość | Opis |
| --- | --- |
| **Alias wejściowy** |Przyjazna nazwa umożliwia w zapytaniu zadanie odwoływać danych wejściowych. |
| **Przestrzeń nazw magistrali usług** |Azure Service Bus przestrzeń nazw, które to kontener dla zestawu jednostek do obsługi komunikatów. Podczas tworzenia nowego Centrum zdarzeń można również utworzyć przestrzeni nazw usługi Service Bus. |
| **Nazwa Centrum zdarzeń** |Nazwa Centrum zdarzeń do użycia jako dane wejściowe. |
| **Nazwa zasad Centrum zdarzeń** |Zasady dostępu współdzielonego, który zapewnia dostęp do Centrum zdarzeń. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia ustawić i klucze dostępu. |
| **Grupy konsumentów Centrum zdarzeń** (opcjonalnie) |Grupy odbiorców do użycia na potrzeby wysyłania danych z Centrum zdarzeń. Jeśli zostanie określona żadna grupa odbiorców, zadanie usługi Stream Analytics korzysta domyślna grupa odbiorców. Firma Microsoft zaleca użycie grupy konsumentów różne dla każdego zadania usługi analiza strumienia. |
| **Format serializacji zdarzeń** |Format serializacji (JSON, CSV lub Avro) przychodzącego strumienia danych. |
| **Kodowanie** | UTF-8 jest obecnie obsługiwany tylko format kodowania. |
| **Kompresja** (opcjonalnie) | Typ kompresji (Brak, GZip i Deflate) przychodzącego strumienia danych. |

Gdy dane pochodzą z Centrum zdarzeń, masz dostęp do następujących pól metadanych w kwerendzie Stream Analytics:

| Właściwość | Opis |
| --- | --- |
| **EventProcessedUtcTime** |Data i czas przetwarzania zdarzenia przez usługę Stream Analytics. |
| **EventEnqueuedUtcTime** |Data i godzina zdarzenia otrzymała centrów zdarzeń. |
| **partitionId** |Identyfikator liczony od zera partycję adapter wejścia. |

Na przykład przy użyciu tych pól, można napisać zapytanie, jak w następującym przykładzie:

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

> [!NOTE]
> Korzystając z Centrum zdarzeń jako punktu końcowego dla tras Centrum IoT, możesz uzyskać dostępu do przy użyciu medadata Centrum IoT [funkcja GetMetadataPropertyValue](https://msdn.microsoft.com/en-us/library/azure/mt793845.aspx).
> 

## <a name="create-data-stream-input-from-iot-hub"></a>Tworzenie elementu wejściowego strumienia danych z Centrum IoT
Centrum Iot Azure to wysoce skalowalna publikowania / subskrypcji zoptymalizowane pod kątem scenariuszach IoT systemem zbierania zdarzeń.

Sygnatura czasowa domyślne zdarzenia pochodzące z Centrum IoT w Stream Analytics jest sygnatury czasowej, które dotarły zdarzenia w Centrum IoT, który jest `EventEnqueuedUtcTime`. Do przetwarzania danych jako strumień przy użyciu sygnatury czasowej w przypadku ładunku, należy użyć [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) — słowo kluczowe.

> [!NOTE]
> Tylko wiadomości wysyłane z `DeviceClient` właściwości mogą być przetwarzane.
> 
> 

### <a name="consumer-groups"></a>Grupy odbiorców
Należy skonfigurować każdy Centrum IoT analizy strumienia, dane wejściowe mają własne grupy odbiorców. Jeśli zadanie zawiera samosprzężenie lub składa się z wielu danych wejściowych, niektóre dane wejściowe mogą odczytać przez więcej niż jeden czytnik poniżej. Taka sytuacja wpływa na liczbę czytników w grupie jednego konsumenta. Aby zapobiec przekroczeniu limitu Centrum IoT Azure pięć czytników dla każdej grupy odbiorców dla każdej partycji, jest najlepszym rozwiązaniem, aby wyznaczyć grupy odbiorców dla każdego zadania usługi analiza strumienia.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Konfigurowanie Centrum IoT jako strumienia danych wejściowych
W poniższej tabeli opisano każdej właściwości w **wprowadzania nowych** bloku w portalu Azure podczas konfigurowania Centrum IoT jako dane wejściowe.

| Właściwość | Opis |
| --- | --- |
| **Alias wejściowy** |Przyjazna nazwa umożliwia w zapytaniu zadanie odwoływać danych wejściowych.|
| **Centrum IoT** |Nazwa centrum IoT można użyć jako danych wejściowych. |
| **punkt końcowy** |Punktu końcowego Centrum IoT.|
| **Nazwa zasady dostępu współdzielonego** |Zasady dostępu współdzielonego, który zapewnia dostęp do Centrum IoT. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia ustawić i klucze dostępu. |
| **Klucz zasady dostępu współdzielonego** |Klucz dostępu współdzielonego, używany do autoryzacji dostępu do Centrum IoT. |
| **Grupy odbiorców** (opcjonalnie) |Grupy odbiorców do użycia na potrzeby wysyłania danych z Centrum IoT. Jeśli zostanie określona żadna grupa odbiorców, zadanie usługi Stream Analytics korzysta domyślna grupa odbiorców. Zalecane jest użycie grupy odbiorców różne dla każdego zadania usługi analiza strumienia. |
| **Format serializacji zdarzeń** |Format serializacji (JSON, CSV lub Avro) przychodzącego strumienia danych. |
| **Kodowanie** |UTF-8 jest obecnie obsługiwany tylko format kodowania. |
| **Kompresja** (opcjonalnie) | Typ kompresji (Brak, GZip i Deflate) przychodzącego strumienia danych. |

Gdy dane pochodzą z Centrum IoT, masz dostęp do następujących pól metadanych w kwerendzie Stream Analytics:

| Właściwość | Opis |
| --- | --- |
| **EventProcessedUtcTime** |Data i godzina, który był przetwarzany zdarzenia. |
| **EventEnqueuedUtcTime** |Data i godzina, które zdarzenie zostało odebrane przez Centrum IoT. |
| **partitionId** |Identyfikator liczony od zera partycję adapter wejścia. |
| **IoTHub.MessageId** | Identyfikator, który służy do skorelowania dwukierunkowej komunikacji w Centrum IoT. |
| **IoTHub.CorrelationId** |Identyfikator, który jest używany w odpowiedzi komunikat i opinie w Centrum IoT. |
| **IoTHub.ConnectionDeviceId** |Identyfikator uwierzytelniania używany do wysłania tej wiadomości. Ta wartość jest dołączana do komunikatów servicebound przez Centrum IoT. |
| **IoTHub.ConnectionDeviceGenerationId** |Identyfikator generacji uwierzytelnionego urządzenia, którego użyto do wysłania tej wiadomości. Ta wartość jest dołączana do komunikatów servicebound przez Centrum IoT. |
| **IoTHub.EnqueuedTime** |Czas, kiedy wiadomość dotarła Centrum IoT. |
| **IoTHub.StreamId** |Właściwość zdarzenie niestandardowe dodane przez urządzenie nadawcy. |


## <a name="create-data-stream-input-from-blob-storage"></a>Tworzenie elementu wejściowego strumienia danych z magazynu obiektów Blob
W przypadku scenariuszy z dużych ilości danych bez struktury mają być przechowywane w chmurze Azure Blob storage oferuje ekonomiczne i skalowalne rozwiązanie. Dane w magazynie obiektów Blob jest zazwyczaj uważana za przechowywanych danych. Jednak będzie można przetwarzać jako strumień danych przez usługę Stream Analytics. Typowy scenariusz w danych wejściowych magazynu obiektów Blob z usługi Stream Analytics jest przetwarzania dziennika. W tym scenariuszu dane telemetryczne została przechwycona z systemu i musi zostać przeanalizowany i przetwarzane w celu wyodrębnienia istotnych danych.

Sygnatura czasowa domyślne zdarzenia magazynu obiektów Blob w Stream Analytics jest sygnatura czasowa czy ostatniej modyfikacji obiektu blob, który jest `BlobLastModifiedUtcTime`. Do przetwarzania danych jako strumień przy użyciu sygnatury czasowej w przypadku ładunku, należy użyć [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) — słowo kluczowe.

Wejść w formacie CSV *wymagają* wiersz nagłówka, aby zdefiniować pól dla zestawu danych. Ponadto wszystkie pola wiersz nagłówka musi być unikatowa.

> [!NOTE]
> Analiza strumienia obsługuje dodawanie zawartości do istniejącego pliku obiektu blob. Analiza strumienia zostaną wyświetlone tylko raz każdego pliku, a wszelkie zmiany wprowadzone w pliku, gdy zadanie może odczytywać dane nie są przetwarzane. Najlepszym rozwiązaniem jest jednocześnie Przekaż wszystkie dane dla pliku blob, a następnie dodaj dodatkowe nowszych zdarzeń do pliku innego, nowych obiektów blob.
> 

### <a name="configure-blob-storage-as-a-data-stream-input"></a>Konfigurowanie magazynu obiektów Blob jako strumienia danych wejściowych

W poniższej tabeli opisano każdej właściwości w **wprowadzania nowych** bloku w portalu Azure podczas konfigurowania magazynu obiektów Blob jako dane wejściowe.

| Właściwość | Opis |
| --- | --- |
| **Alias wejściowy** | Przyjazna nazwa umożliwia w zapytaniu zadanie odwoływać danych wejściowych. |
| **Konto magazynu** | Nazwa konta magazynu, w którym znajdują się pliki obiektu blob. |
| **Klucz konta magazynu** | Klucz tajny, skojarzone z kontem magazynu. |
| **Kontener** | Kontener obiektu blob danych wejściowych. Kontenery umożliwiają logiczne grupowanie dla obiektów blob przechowywanych w usłudze Microsoft Azure Blob. Przekazywanie obiektu blob do usługi magazynu obiektów Blob platformy Azure, należy określić kontener dla tego obiektu blob. |
| **Wzorzec ścieżki** (opcjonalnie) | Ścieżka plików używana do lokalizowania obiektów blob w określonym kontenerze. W ścieżce można określić co najmniej jedno wystąpienie następujących trzech zmiennych: `{date}`, `{time}`, lub `{partition}`<br/><br/>Przykład 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Przykład 2: `cluster1/logs/{date}`<br/><br/>`*` Znak nie jest dozwolona wartość prefiksu ścieżki. Jedyne prawidłowe <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">znaków obiektów blob platformy Azure</a> są dozwolone. |
| **Format daty** (opcjonalnie) | Jeśli użyjesz zmiennej daty w ścieżce, format daty, w którym pliki są organizowane. Przykład: `YYYY/MM/DD` |
| **Format czasu** (opcjonalnie) |  Jeśli użyjesz zmiennej czasu w ścieżce, format czasu, w którym pliki są organizowane. Obecnie jest to jedyna obsługiwana wartość `HH`. |
| **Format serializacji zdarzeń** | Format serializacji (JSON, CSV lub Avro) dla przychodzących strumieni danych. |
| **Kodowanie** | Dla woluminu CSV i JSON UTF-8 jest obecnie obsługiwany tylko format kodowania. |
| **Kompresja** (opcjonalnie) | Typ kompresji (Brak, GZip i Deflate) przychodzącego strumienia danych. |

Gdy dane pochodzą ze źródła magazynu obiektów Blob, masz dostęp do następujących pól metadanych w kwerendzie Stream Analytics:

| Właściwość | Opis |
| --- | --- |
| **BlobName** |Nazwa zdarzenia dostarczone z blob danych wejściowych. |
| **EventProcessedUtcTime** |Data i czas przetwarzania zdarzenia przez usługę Stream Analytics. |
| **BlobLastModifiedUtcTime** |Data i godzina ostatniej modyfikacji obiektu blob. |
| **partitionId** |Identyfikator liczony od zera partycję adapter wejścia. |

Na przykład przy użyciu tych pól, można napisać zapytanie, jak w następującym przykładzie:

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
Znasz opcji połączenia danych na platformie Azure dla Twojego zadania usługi analiza strumienia. Aby dowiedzieć się więcej na temat usługi Stream Analytics, zobacz:

* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
