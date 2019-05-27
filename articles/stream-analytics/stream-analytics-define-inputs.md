---
title: Stream data jako dane wejściowe do usługi Azure Stream Analytics
description: Więcej informacji na temat konfigurowania połączenia danych w usłudze Azure Stream Analytics. Dane wejściowe zawierają strumień danych zdarzeń, a także odwoływać się do danych.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 420705ef6b2e38d147b7033d2fb3ad57bbc216ac
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159287"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Stream data jako dane wejściowe do usługi Stream Analytics

Stream Analytics ma najwyższej jakości Integracja ze strumieniami danych na platformie Azure jako dane wejściowe z trzech rodzajów zasobów:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Te zasoby danych wejściowych może znajdować się w tej samej subskrypcji platformy Azure jako zadania usługi Stream Analytics lub w innej subskrypcji.

### <a name="compression"></a>Kompresja
Stream Analytics obsługuje kompresję dla wszystkich źródeł danych wejściowych strumienia danych. Kompresja obecnie obsługiwane typy to: Brak GZip i kompresji Deflate. Obsługa kompresji nie jest dostępna dla danych referencyjnych. Jeśli format wejściowy to dane Avro jest skompresowany, zapewniona jest obsługa sposób niewidoczny dla użytkownika. Nie musisz określić typ kompresji serializacji Avro. 

## <a name="create-edit-or-test-inputs"></a>Tworzenie, edytowanie i testowanie danych wejściowych
Możesz użyć [witryny Azure portal](https://portal.azure.com) do [Utwórz nowe dane wejściowe](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal#configure-job-input) i Wyświetl lub Edytuj istniejące dane wejściowe zadania przesyłania strumieniowego. Można również Testuj połączenia wejściowe i [zapytania testowe](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-manage-job#test-your-query) z przykładowymi danymi. Podczas pisywania zapytania spowoduje wyświetlenie listy danych wejściowych w klauzuli FROM. Można uzyskać listę dostępnych danych wejściowych z **zapytania** strony w portalu. Jeśli użytkownik chce używać wielu danych wejściowych, możesz to zrobić `JOIN` je lub zapisać wiele `SELECT` zapytania.


## <a name="stream-data-from-event-hubs"></a>Przesyłanie strumieniowe danych z usługi Event Hubs

Usługa Azure Event Hubs oferuje wysoce skalowalną publikowania/subskrybowania ingestors zdarzeń. Centrum zdarzeń można zbierać miliony zdarzeń na sekundę, tak że można przetwarzać oraz analizować duże ilości danych wytworzonych przez podłączone urządzenia i aplikacje. Razem usługi Event Hubs i Stream Analytics zapewnia rozwiązania end-to-end Analityki w czasie rzeczywistym. Usługa Event Hubs umożliwia źródła danych zdarzeń na platformę Azure w czasie rzeczywistym, i zadania usługi Stream Analytics może przetwarzać zdarzenia w czasie rzeczywistym. Na przykład możesz wysłać kliknięć w sieci web, odczyty czujników lub online dziennika zdarzeń do usługi Event Hubs. Następnie można utworzyć zadania usługi Stream Analytics na potrzeby usługi Event Hubs jako strumieni danych wejściowych w czasie rzeczywistym filtrowania, agregowania i korelacji.

`EventEnqueuedUtcTime` jest to sygnatura czasowa odebrania zdarzeń w Centrum zdarzeń i jest to domyślny sygnatura czasowa zdarzenia pochodzące z usługi Event Hubs do usługi Stream Analytics. Do przetwarzania danych w formie strumienia za pomocą znacznika czasu w przypadku ładunek, należy użyć [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) — słowo kluczowe.

### <a name="consumer-groups"></a>Grupy odbiorców
Należy skonfigurować każdy Centrum zdarzeń usługi Stream Analytics, danych wejściowych w celu ma swoje własne grupy odbiorców. Gdy zadanie zawiera samosprzężenie lub ma wielu danych wejściowych, niektórych danych wejściowych może przeczytać przez więcej niż jeden czytnik podrzędne. Taka sytuacja wpływa na liczbę czytników w grupie jednego konsumenta. Aby zapobiec przekroczeniu limitu usługi Event Hubs pięć czytników dla każdej grupy odbiorców dla każdej partycji, jest najlepszym rozwiązaniem, aby wyznaczyć grupy odbiorców dla każdego zadania usługi Stream Analytics. Istnieje również limit 20 grup odbiorców dla Centrum zdarzeń. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługą Azure Stream Analytics w danych wejściowych](stream-analytics-troubleshoot-input.md).

### <a name="stream-data-from-event-hubs"></a>Przesyłanie strumieniowe danych z usługi Event Hubs
W poniższej tabeli opisano każdej właściwości w **nowe dane wejściowe** strony w witrynie Azure portal do strumienia danych wejściowych z Centrum zdarzeń:

| Właściwość | Opis |
| --- | --- |
| **Alias wejściowy** |Przyjazna nazwa używanego w zapytaniu zadania do odwołania, to dane wejściowe. |
| **Subskrypcja** | Wybierz subskrypcję, w której istnieje zasób Centrum zdarzeń. | 
| **Przestrzeń nazw Centrum zdarzeń** | Przestrzeń nazw Centrum zdarzeń to kontener dla zestawu jednostek do obsługi komunikatów. Podczas tworzenia nowego Centrum zdarzeń, możesz również utworzyć przestrzeń nazw. |
| **Nazwa Centrum zdarzeń** | Nazwa Centrum zdarzeń, aby użyć jako danych wejściowych. |
| **Nazwa zasad Centrum zdarzeń** | Zasady dostępu współdzielonego, który zapewnia dostęp do Centrum zdarzeń. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia, ustaw i klucze dostępu. Ta opcja jest automatycznie wypełniane, chyba że wybierzesz opcję Tak, aby określić ustawienia Centrum zdarzeń ręcznie.|
| **Grupa konsumentów Centrum zdarzeń** (zalecane) | Zdecydowanie zaleca się używać grupy odbiorców różne dla każdego zadania usługi Stream Analytics. Ten ciąg identyfikuje grupy odbiorców do użycia w celu pozyskiwania danych z Centrum zdarzeń. Jeśli zostanie określona żadna grupa odbiorców, zadanie usługi Stream Analytics używa $Default grupy odbiorców.  |
| **Format serializacji zdarzeń** | Format serializacji (JSON, CSV lub Avro) przychodzącego strumienia danych.  Upewnij się, zgodnie ze specyfikacją formatu JSON, a nie zawiera cyfry 0 w przypadku liczb dziesiętnych. |
| **Kodowanie** | UTF-8 jest obecnie obsługiwany tylko format kodowania. |
| **Typ kompresji zdarzenia** | Typ kompresji używany do odczytu przychodzącego strumienia danych, takich jak brak (ustawienie domyślne), GZip lub Deflate. |

Gdy dane pochodzą ze strumienia danych wejściowych Centrum zdarzeń, masz dostęp do pola metadanych w zapytaniu usługi Stream Analytics:

| Właściwość | Opis |
| --- | --- |
| **EventProcessedUtcTime** |Data i godzina, że zdarzenie zostało przetworzone przez usługę Stream Analytics. |
| **EventEnqueuedUtcTime** |Data i godzina, które zdarzenie zostało odebrane przez usługi Event Hubs. |
| **Identyfikator partycji** |Identyfikator partycji liczony od zera dla danych wejściowych karty. |

Na przykład korzystając z tych pól, można napisać zapytanie, jak w poniższym przykładzie:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Korzystając z Centrum zdarzeń jako punkt końcowy dla tras Centrum IoT, mogą uzyskać dostęp do metadanych usługi IoT Hub przy użyciu [funkcja GetMetadataPropertyValue](https://msdn.microsoft.com/library/azure/mt793845.aspx).
> 

## <a name="stream-data-from-iot-hub"></a>Stream dane z usługi IoT Hub
Usługa Azure Iot Hub to wysoko skalowalna publikowania/subskrybowania o dużych możliwościach skalowania zdarzeń zoptymalizowane pod kątem scenariuszy IoT.

Domyślne sygnatura czasowa zdarzenia pochodzące z usługi IoT Hub w usłudze Stream Analytics jest sygnatura czasowa, która zdarzenie dostarczone w usłudze IoT Hub, który jest `EventEnqueuedUtcTime`. Do przetwarzania danych w formie strumienia za pomocą znacznika czasu w przypadku ładunek, należy użyć [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) — słowo kluczowe.

### <a name="consumer-groups"></a>Grupy odbiorców
Należy skonfigurować każdy Stream Analytics usługi IoT Hub jako wejście do ma swoje własne grupy odbiorców. Kiedy zadanie zawiera samosprzężenie lub ma on wiele danych wejściowych, dane wejściowe mogą odczytać przez więcej niż jeden czytnik podrzędne. Taka sytuacja wpływa na liczbę czytników w grupie jednego konsumenta. Aby uniknąć przekracza limit usługi Azure IoT Hub pięć czytników dla każdej grupy odbiorców dla każdej partycji, jest najlepszym rozwiązaniem, aby wyznaczyć grupy odbiorców dla każdego zadania usługi Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Konfigurowanie usługi IoT Hub jako strumienia danych wejściowych
W poniższej tabeli opisano każdej właściwości w **nowe dane wejściowe** strony w witrynie Azure portal po skonfigurowaniu usługi IoT Hub jako strumienia danych wejściowych.

| Właściwość | Opis |
| --- | --- |
| **Alias wejściowy** | Przyjazna nazwa używanego w zapytaniu zadania do odwołania, to dane wejściowe.|
| **Subskrypcja** | Wybierz subskrypcję, w której istnieje zasób usługi IoT Hub. | 
| **IoT Hub** | Nazwa centrum IoT Hub do użycia jako dane wejściowe. |
| **Punkt końcowy** | Punkt końcowy usługi IoT Hub.|
| **Nazwa zasad dostępu współużytkowanego** | Zasady dostępu współdzielonego, który zapewnia dostęp do usługi IoT Hub. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia, ustaw i klucze dostępu. |
| **Klucz zasad dostępu współużytkowanego** | Klucz dostępu współdzielonego, używany do autoryzowania dostępu do usługi IoT Hub.  Ta opcja jest automatycznie wypełniane w, o ile nie zostanie wybrana opcja, aby określić ustawienia Centrum Iot Hub ręcznie. |
| **Grupy konsumentów** | Zdecydowanie zaleca się używać grupy odbiorców różne dla każdego zadania usługi Stream Analytics. Grupa odbiorców jest używana do pozyskiwania danych z usługi IoT Hub. Stream Analytics używa grupa użytkowników $Default, chyba że określono inaczej.  |
| **Format serializacji zdarzeń** | Format serializacji (JSON, CSV lub Avro) przychodzącego strumienia danych.  Upewnij się, zgodnie ze specyfikacją formatu JSON, a nie zawiera cyfry 0 w przypadku liczb dziesiętnych. |
| **Kodowanie** | UTF-8 jest obecnie obsługiwany tylko format kodowania. |
| **Typ kompresji zdarzenia** | Typ kompresji używany do odczytu przychodzącego strumienia danych, takich jak brak (ustawienie domyślne), GZip lub Deflate. |


Gdy używasz przesyłanie strumieniowe danych z usługi IoT Hub, masz dostęp do pola metadanych w zapytaniu usługi Stream Analytics:

| Właściwość | Opis |
| --- | --- |
| **EventProcessedUtcTime** | Data i godzina, która została przetworzona zdarzenia. |
| **EventEnqueuedUtcTime** | Data i godzina odebrania zdarzeń przez usługę IoT Hub. |
| **Identyfikator partycji** | Identyfikator partycji liczony od zera dla danych wejściowych karty. |
| **IoTHub.MessageId** | Identyfikator, który służy do skorelowania dwukierunkowej komunikacji w usłudze IoT Hub. |
| **IoTHub.CorrelationId** | Identyfikator, który jest używany w odpowiedzi na wiadomość i opinii w usłudze IoT Hub. |
| **IoTHub.ConnectionDeviceId** | Identyfikator uwierzytelniania używany do wysyłania tego komunikatu. Ta wartość jest dołączana do wiadomości powiązań usługi w usłudze IoT Hub. |
| **IoTHub.ConnectionDeviceGenerationId** | Identyfikator generacji uwierzytelnionego urządzenia, który został użyty do wysłania tej wiadomości. Ta wartość jest dołączana do wiadomości powiązań usługi w usłudze IoT Hub. |
| **IoTHub.EnqueuedTime** | Czas, kiedy wiadomość została odebrana przez usługę IoT Hub. |


## <a name="stream-data-from-blob-storage"></a>Stream dane z magazynu obiektów Blob
W przypadku scenariuszy z dużych ilości danych bez struktury, do przechowywania w chmurze usługi Azure Blob storage oferuje ekonomiczne i skalowalne rozwiązanie. Dane w magazynie obiektów Blob jest zazwyczaj uważana za dane magazynowane; Jednak dane obiektów blob mogą być przetwarzane jako strumień danych przez usługę Stream Analytics. 

Przetwarzania dziennika to powszechnie używany scenariusz dotyczące korzystania z danych wejściowych z magazynu obiektów Blob za pomocą usługi Stream Analytics. W tym scenariuszu pliki danych telemetrycznych przechwycone z systemu i muszą zostać przeanalizowany i przetwarzane w celu wyodrębnienia istotnych danych.

Domyślne sygnatura czasowa zdarzenia usługi Blob storage w usłudze Stream Analytics jest sygnatura czasowa czy ostatniej modyfikacji obiektu blob, który jest `BlobLastModifiedUtcTime`. Do przetwarzania danych w formie strumienia za pomocą znacznika czasu w przypadku ładunek, należy użyć [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) — słowo kluczowe. Zadanie usługi Stream Analytics pobiera dane z usługi Azure Blob storage w danych wejściowych co sekundę Jeśli pliku obiektu blob jest dostępna. Plik obiektu blob jest niedostępny, czy wykładniczego wycofywania z opóźnieniem maksymalny czas w 90 sekund.

Wejść w formacie CSV *wymagają* wiersz nagłówka do definiowania pól dla zestawu danych i wszystkie pola wiersz nagłówka musi być unikatowa.

Stream Analytics nie obsługuje obecnie deserializacji komunikaty AVRO, generowane przez Przechwytywanie Centrum zdarzeń lub niestandardowy punkt końcowy kontenera magazynu Azure Centrum IoT.

> [!NOTE]
> Stream Analytics nie obsługuje dodawania zawartości do istniejącego pliku obiektu blob. Stream Analytics będzie wyświetlać każdy plik tylko raz, a wszelkie zmiany, które występują w pliku, gdy zadanie ma odczytywać dane nie są przetwarzane. Najlepszym rozwiązaniem jest przekazywanie wszystkich danych na potrzeby plik obiektów blob na raz, a następnie dodaj dodatkowe nowszych zdarzeń na plik obiektów blob innego, nowe.
> 

Przekazywanie dużej liczby obiektów blob na raz może spowodować pomijają odczytywanie kilka obiektów blob w rzadkich przypadkach w usłudze Stream Analytics. Zalecane jest przekazywanie obiektów blob co najmniej 2 sekundy od siebie do magazynu obiektów Blob. Jeśli ta opcja nie jest możliwe, można użyć usługi Event Hubs do dużych woluminów strumienia zdarzeń. 

### <a name="configure-blob-storage-as-a-stream-input"></a>Konfigurowanie magazynu obiektów Blob jako dane wejściowe strumienia 

W poniższej tabeli opisano każdej właściwości w **nowe dane wejściowe** strony w witrynie Azure portal po skonfigurowaniu magazynu obiektów Blob jako dane wejściowe strumienia.

| Właściwość | Opis |
| --- | --- |
| **Alias wejściowy** | Przyjazna nazwa używanego w zapytaniu zadania do odwołania, to dane wejściowe. |
| **Subskrypcja** | Wybierz subskrypcję, w której istnieje zasób usługi IoT Hub. | 
| **Konto magazynu** | Nazwa konta magazynu, w którym znajdują się pliki obiektów blob. |
| **Klucz konta magazynu** | Klucz tajny skojarzony z kontem magazynu. Ta opcja jest automatycznie wypełniona w, chyba że zaznaczono opcję, aby zapewnić ustawienia magazynu obiektów Blob ręcznie. |
| **Kontener** | Kontener obiektów blob, dane wejściowe. Kontenery umożliwiają logiczne grupowanie obiektów blob przechowywanych w usłudze Microsoft Azure Blob. Podczas przekazywania obiektu blob do usługi Azure Blob storage, należy określić kontener dla tego obiektu blob. Możesz wybrać dowolną **Użyj istniejącej** kontenera lub **Utwórz nową** mieć utworzono nowy kontener.|
| **Wzorzec ścieżki** (opcjonalnie) | Ścieżka pliku używana do lokalizowania obiektów blob w określonym kontenerze. W ścieżce można określić co najmniej jedno wystąpienie następujących trzech zmiennych: `{date}`, `{time}`, lub `{partition}`<br/><br/>Przykład 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Przykład 2: `cluster1/logs/{date}`<br/><br/>`*` Znak nie jest dozwolona wartość prefiksu ścieżki. Jedyne prawidłowe <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">znaków obiektów blob platformy Azure</a> są dozwolone. Nie zawiera nazwy kontenera ani nazwy plików. |
| **Format daty** (opcjonalnie) | Jeśli używasz Zmienna daty w ścieżce, format daty, w której pliki są organizowane. Przykład: `YYYY/MM/DD` |
| **Format czasu** (opcjonalnie) |  Jeśli w ścieżce, format czasu, w której pliki są organizowane za pomocą zmiennej czasu. Obecnie jest to jedyna obsługiwana wartość `HH` godzin. |
| **Format serializacji zdarzeń** | Format serializacji (JSON, CSV lub Avro) przychodzącego strumienia danych.  Upewnij się, zgodnie ze specyfikacją formatu JSON, a nie zawiera cyfry 0 w przypadku liczb dziesiętnych. |
| **Kodowanie** | Dla woluminu CSV i JSON UTF-8 jest obecnie obsługiwany tylko format kodowania. |
| **Kompresja** | Typ kompresji używany do odczytu przychodzącego strumienia danych, takich jak brak (ustawienie domyślne), GZip lub Deflate. |

Gdy dane pochodzą ze źródła do magazynu obiektów Blob, masz dostęp do pola metadanych w zapytaniu usługi Stream Analytics:

| Właściwość | Opis |
| --- | --- |
| **BlobName** |Nazwa wejściowego obiektu blob, do którego zdarzenie. |
| **EventProcessedUtcTime** |Data i godzina, że zdarzenie zostało przetworzone przez usługę Stream Analytics. |
| **BlobLastModifiedUtcTime** |Data i godzina ostatniej modyfikacji obiektu blob. |
| **Identyfikator partycji** |Identyfikator partycji liczony od zera dla danych wejściowych karty. |

Na przykład korzystając z tych pól, można napisać zapytanie, jak w poniższym przykładzie:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Szybki start: Tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
