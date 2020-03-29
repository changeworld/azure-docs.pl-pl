---
title: Przesyłanie strumieniowe danych jako danych wejściowych do usługi Azure Stream Analytics
description: Dowiedz się więcej o konfigurowaniu połączenia danych w usłudze Azure Stream Analytics. Dane wejściowe obejmują strumień danych ze zdarzeń, a także dane referencyjne.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 388f43fec9242f6a4b448483d9486aa4413d2612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254471"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Przesyłanie strumieniowe danych jako danych wejściowych do usługi Stream Analytics

Usługa Stream Analytics ma pierwszorzędną integrację ze strumieniami danych platformy Azure jako dane wejściowe z trzech rodzajów zasobów:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Magazyn obiektów Blob platformy Azure](https://azure.microsoft.com/services/storage/blobs/) 

Te zasoby wejściowe mogą być dostępne w tej samej subskrypcji platformy Azure, co zadanie usługi Stream Analytics lub inna subskrypcja.

### <a name="compression"></a>Kompresja

Usługa Stream Analytics obsługuje kompresję we wszystkich źródłach wejściowych strumienia danych. Obsługiwane typy kompresji to: Brak, GZip i Kompresja Deflate. Obsługa kompresji nie jest dostępna dla danych referencyjnych. Jeśli formatem wejściowym są skompresowane dane Avro, są obsługiwane w sposób przezroczysty. Nie musisz określać typu kompresji za pomocą serializacji Avro. 

## <a name="create-edit-or-test-inputs"></a>Tworzenie, edytowanie lub testowanie danych wejściowych

Za pomocą [portalu Azure,](stream-analytics-quick-create-portal.md) [programu Visual Studio](stream-analytics-quick-create-vs.md)i programu Visual Studio [Code](quick-create-vs-code.md) można dodawać i wyświetlać lub edytować istniejące dane wejściowe w zadaniu przesyłania strumieniowego. Można również przetestować połączenia wejściowe i [zapytania testowe](stream-analytics-manage-job.md#test-your-query) na podstawie przykładowych danych z portalu Azure, [programu Visual Studio](stream-analytics-vs-tools-local-run.md)i programu Visual Studio [Code.](visual-studio-code-local-run.md) Podczas pisania kwerendy, można wyświetlić listę danych wejściowych w klauzuli FROM. Listę dostępnych danych wejściowych można uzyskać ze strony **Zapytanie** w portalu. Jeśli chcesz użyć wielu danych wejściowych, `JOIN` można `SELECT` je lub napisać wiele zapytań.


## <a name="stream-data-from-event-hubs"></a>Przesyłanie strumieniowe danych z usługi Event Hubs

Usługa Azure Event Hubs zapewnia wysoce skalowalne publikowanie i subskrybowanie zdarzeń. Centrum zdarzeń może zbierać miliony zdarzeń na sekundę, dzięki czemu można przetwarzać i analizować ogromne ilości danych wytwarzanych przez podłączone urządzenia i aplikacje. Razem, Event Hubs i Stream Analytics zapewniają kompleksowe rozwiązanie do analizy w czasie rzeczywistym. Centra zdarzeń umożliwiają przesyłanie zdarzeń na platformę Azure w czasie rzeczywistym, a zadania usługi Stream Analytics mogą przetwarzać te zdarzenia w czasie rzeczywistym. Na przykład można wysyłać kliknięcia w sieci Web, odczyty czujników lub zdarzenia dziennika online do centrum zdarzeń. Następnie można utworzyć zadania usługi Stream Analytics, aby używać centrów zdarzeń jako strumieni danych wejściowych do filtrowania, agregowania i korelacji w czasie rzeczywistym.

`EventEnqueuedUtcTime`jest sygnaturą czasową zdarzenia w centrum zdarzeń i jest domyślną sygnaturą czasową zdarzeń pochodzących z centrów zdarzeń do analizy strumienia. Aby przetworzyć dane jako strumień przy użyciu sygnatury czasowej w ładunku zdarzenia, należy użyć słowa kluczowego [TIMESTAMP BY.](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)

### <a name="event-hubs-consumer-groups"></a>Grupy konsumentów centrów zdarzeń

Należy skonfigurować każde wejście wejściowe centrum zdarzeń usługi Stream Analytics, aby mieć własną grupę odbiorców. Gdy zadanie zawiera samoskładnie lub ma wiele danych wejściowych, niektóre dane wejściowe mogą być odczytywane przez więcej niż jeden czytnik w dół. Sytuacja ta wpływa na liczbę czytników w jednej grupie konsumentów. Aby uniknąć przekroczenia limitu usługi Event Hubs pięciu czytników na grupę odbiorców na partycję, najlepszym rozwiązaniem jest wyznaczenie grupy konsumentów dla każdego zadania usługi Stream Analytics. Istnieje również limit 20 grup odbiorców dla centrum zdarzeń warstwy standardowej. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z danymi wejściowymi usługi Azure Stream Analytics](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Tworzenie danych wejściowych z Centrów zdarzeń

W poniższej tabeli wyjaśniono każdą właściwość na stronie **Nowe dane wejściowe** w witrynie Azure Portal, aby przesyłać strumieniowo dane wejściowe z centrum zdarzeń:

| Właściwość | Opis |
| --- | --- |
| **Alias wejściowy** |Przyjazna nazwa używana w kwerendzie zadania do odwoływania się do tego danych wejściowych. |
| **Subskrypcja** | Wybierz subskrypcję, w której istnieje zasób Centrum zdarzeń. | 
| **Przestrzeń nazw centrum zdarzeń** | Obszar nazw Centrum zdarzeń jest kontenerem dla zestawu jednostek obsługi wiadomości. Podczas tworzenia nowego centrum zdarzeń można również utworzyć obszar nazw. |
| **Nazwa Centrum zdarzeń** | Nazwa centrum zdarzeń do użycia jako dane wejściowe. |
| **Nazwa zasad centrum zdarzeń** | Zasady dostępu współdzielonego, który zapewnia dostęp do Centrum zdarzeń. Każda zasada dostępu współdzielonego ma ustawioną nazwę, uprawnienia i klucze dostępu. Ta opcja jest wypełniana automatycznie, chyba że wybierzesz opcję ręcznego podania ustawień Centrum zdarzeń.|
| **Grupa odbiorców Centrum zdarzeń** (zalecane) | Zdecydowanie zaleca się użycie odrębnej grupy odbiorców dla każdego zadania usługi Stream Analytics. Ten ciąg identyfikuje grupę odbiorców, która ma być używana do pozyskiwania danych z Centrum zdarzeń. Jeśli nie określono żadnej grupy odbiorców, zadanie usługi Stream Analytics używa $Default grupy odbiorców.  |
| **Klucz partycji** | Jeśli dane wejściowe są podzielone na partycje przez właściwość, można dodać nazwę tej właściwości. Klucze partycji są opcjonalne i służy do zwiększenia wydajności kwerendy, jeśli zawiera klauzulę PARTITION BY lub GROUP BY w tej właściwości. |
| **Format serializacji zdarzeń** | Format serializacji (JSON, CSV, Avro lub [Inne (Protobuf, XML, zastrzeżone ...)](custom-deserializer.md)) przychodzącego strumienia danych.  Upewnij się, że format JSON jest zgodny ze specyfikacją i nie zawiera wiodącej 0 dla liczb dziesiętnych. |
| **Kodowanie** | UTF-8 jest obecnie jedynym obsługiwanym formatem kodowania. |
| **Typ kompresji zdarzeń** | Typ kompresji używany do odczytywania przychodzącego strumienia danych, taki jak Brak (domyślny), GZip lub Deflate. |

Gdy dane pochodzą z danych wejściowych usługi Event Hub, masz dostęp do następujących pól metadanych w zapytaniu usługi Stream Analytics:

| Właściwość | Opis |
| --- | --- |
| **EventProcessedUtcTime** |Data i godzina przetworzenia zdarzenia przez analizę stream analytics. |
| **EventEnqueuedUtcTime** |Data i godzina odebrana przez usługi Event Hubs. |
| **Partitionid** |Identyfikator partycji zerowej dla karty wejściowej. |

Na przykład za pomocą tych pól można napisać kwerendę, podobnie jak w poniższym przykładzie:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Korzystając z Centrum zdarzeń jako punktu końcowego dla tras centrum IoT, można uzyskać dostęp do metadanych Usługi IoT Hub przy użyciu [funkcji GetMetadataPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue).
> 

## <a name="stream-data-from-iot-hub"></a>Przesyłanie strumieniowe danych z Usługi IoT Hub

Usługa Azure IoT Hub jest wysoce skalowalnym zdarzeniem publikowania i subskrybowania zoptymalizowanym pod kątem scenariuszy IoT.

Domyślna sygnatura czasowa zdarzeń pochodzących z Usługi IoT Hub w usłudze Stream Analytics `EventEnqueuedUtcTime`to sygnatura czasowa, do której zdarzenie dotarło do Centrum IoT Hub, czyli . Aby przetworzyć dane jako strumień przy użyciu sygnatury czasowej w ładunku zdarzenia, należy użyć słowa kluczowego [TIMESTAMP BY.](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)

### <a name="iot-hub-consumer-groups"></a>Grupy konsumentów Iot Hub

Należy skonfigurować każde wejście wejścia Usługi IoT usługi Usługi Stream Analytics, aby mieć własną grupę odbiorców. Gdy zadanie zawiera samoskładnie lub gdy ma wiele danych wejściowych, niektóre dane wejściowe mogą być odczytywane przez więcej niż jeden czytnik w dół. Sytuacja ta wpływa na liczbę czytników w jednej grupie konsumentów. Aby uniknąć przekroczenia limitu usługi Azure IoT Hub pięciu czytników na grupę odbiorców na partycję, najlepszym rozwiązaniem jest wyznaczenie grupy konsumentów dla każdego zadania usługi Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Konfigurowanie centrum IoT Hub jako danych wejściowych

W poniższej tabeli wyjaśniono każdą właściwość na stronie **Nowe dane wejściowe** w witrynie Azure portal podczas konfigurowania usługi IoT Hub jako danych wejściowych strumienia.

| Właściwość | Opis |
| --- | --- |
| **Alias wejściowy** | Przyjazna nazwa używana w kwerendzie zadania do odwoływania się do tego danych wejściowych.|
| **Subskrypcja** | Wybierz subskrypcję, w której istnieje zasób Usługi IoT Hub. | 
| **Iot** | Nazwa Usługi IoT Hub do użycia jako dane wejściowe. |
| **Punktu końcowego** | Punkt końcowy dla Centrum IoT.|
| **Nazwa zasad dostępu współdzielonego** | Zasada dostępu współdzielonego, która zapewnia dostęp do Centrum IoT. Każda zasada dostępu współdzielonego ma ustawioną nazwę, uprawnienia i klucze dostępu. |
| **Klucz zasad dostępu współdzielonego** | Klucz dostępu współdzielonego używany do autoryzowania dostępu do usługi IoT Hub.  Ta opcja jest wypełniana automatycznie, chyba że wybierzesz opcję ręcznego podania ustawień Iot Hub. |
| **Grupa konsumentów** | Zdecydowanie zaleca się używanie innej grupy odbiorców dla każdego zadania usługi Stream Analytics. Grupa odbiorców służy do pozyskiwania danych z Usługi IoT Hub. Usługa Stream Analytics używa $Default grupy odbiorców, chyba że określisz inaczej.  |
| **Klucz partycji** | Jeśli dane wejściowe są podzielone na partycje przez właściwość, można dodać nazwę tej właściwości. Klucze partycji są opcjonalne i służy do zwiększenia wydajności kwerendy, jeśli zawiera klauzulę PARTITION BY lub GROUP BY w tej właściwości. |
| **Format serializacji zdarzeń** | Format serializacji (JSON, CSV, Avro lub [Inne (Protobuf, XML, zastrzeżone ...)](custom-deserializer.md)) przychodzącego strumienia danych.  Upewnij się, że format JSON jest zgodny ze specyfikacją i nie zawiera wiodącej 0 dla liczb dziesiętnych. |
| **Kodowanie** | UTF-8 jest obecnie jedynym obsługiwanym formatem kodowania. |
| **Typ kompresji zdarzeń** | Typ kompresji używany do odczytywania przychodzącego strumienia danych, taki jak Brak (domyślny), GZip lub Deflate. |


Korzystając z danych strumieniowych z usługi IoT Hub, masz dostęp do następujących pól metadanych w zapytaniu usługi Stream Analytics:

| Właściwość | Opis |
| --- | --- |
| **EventProcessedUtcTime** | Data i godzina przetworzenia zdarzenia. |
| **EventEnqueuedUtcTime** | Data i godzina odebrana przez centrum IoT Hub. |
| **Partitionid** | Identyfikator partycji zerowej dla karty wejściowej. |
| **Identyfikator IoTHub.MessageId** | Identyfikator używany do skorelowania komunikacji dwukierunkowej w centrum IoT Hub. |
| **Identyfikator IoTHub.CorrelationId** | Identyfikator używany w odpowiedziach na wiadomości i opinie w Centrum IoT. |
| **Identyfikator IoTHub.ConnectionDeviceId** | Identyfikator uwierzytelniania używany do wysyłania tej wiadomości. Ta wartość jest wybity na servicebound wiadomości przez Centrum IoT Hub. |
| **Identyfikator IoTHub.ConnectionDeviceGenerationId** | Identyfikator generacji uwierzytelnionego urządzenia, który został użyty do wysłania tej wiadomości. Ta wartość jest wybity na servicebound wiadomości przez Centrum IoT Hub. |
| **IoTHub.EnqueuedCzas** | Czas, kiedy wiadomość została odebrana przez Centrum IoT. |


## <a name="stream-data-from-blob-storage"></a>Przesyłanie strumieniowe danych z magazynu obiektów Blob
W scenariuszach z dużą ilością nieustrukturyzowanych danych do przechowywania w chmurze magazyn obiektów Blob platformy Azure oferuje ekonomiczne i skalowalne rozwiązanie. Dane w magazynie obiektów Blob są zwykle uważane za dane w spoczynku; jednak dane obiektów blob mogą być przetwarzane jako strumień danych przez usługi Stream Analytics. 

Przetwarzanie dziennika jest powszechnie używanym scenariuszem przy użyciu danych wejściowych magazynu obiektów Blob za pomocą usługi Stream Analytics. W tym scenariuszu pliki danych telemetrycznych zostały przechwycone z systemu i muszą być analizowane i przetwarzane w celu wyodrębnienia znaczących danych.

Domyślna sygnatura czasowa zdarzeń magazynu obiektów Blob w usłudze Stream Analytics `BlobLastModifiedUtcTime`jest sygnaturą czasową, która została ostatnio zmodyfikowana, czyli . Jeśli obiekt blob zostanie przekazany do konta magazynu o godzinie 13:00, a zadanie usługi Azure Stream Analytics zostanie uruchomione przy użyciu opcji *Teraz* o godzinie 13:01, obiekt blob nie zostanie pobrany, ponieważ jego zmodyfikowany czas wykracza poza okres wykonywania zadania.

Jeśli obiekt blob zostanie przekazany do kontenera konta magazynu o godzinie 13:00, a zadanie usługi Azure Stream Analytics zostanie uruchomione przy użyciu *czasu niestandardowego* o godzinie 13:00 lub wcześniej, obiekt blob zostanie pobrany, gdy jego zmodyfikowany czas mieści się w okresie wykonywania zadania.

Jeśli zadanie usługi Azure Stream Analytics zostanie uruchomione przy użyciu *teraz* o godzinie 13:00, a obiekt blob zostanie przekazany do kontenera konta magazynu o godzinie 13:01, usługa Azure Stream Analytics odbierze obiekt blob.

Aby przetworzyć dane jako strumień przy użyciu sygnatury czasowej w ładunku zdarzenia, należy użyć słowa kluczowego [TIMESTAMP BY.](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) Zadanie usługi Stream Analytics pobiera dane z danych wejściowych magazynu obiektów Blob platformy Azure co sekundę, jeśli plik obiektu blob jest dostępny. Jeśli plik obiektu blob jest niedostępny, istnieje wykładnicze wycofywanie z maksymalnym opóźnieniem czasowym 90 sekund.

Dane wejściowe w formacie CSV wymagają wiersza nagłówka do definiowania pól dla zestawu danych, a wszystkie pola wierszy nagłówka muszą być unikatowe.

> [!NOTE]
> Usługa Stream Analytics nie obsługuje dodawania zawartości do istniejącego pliku obiektu blob. Usługa Stream Analytics wyświetli każdy plik tylko raz, a wszelkie zmiany, które występują w pliku po przeczytaniu danych przez zadanie, nie są przetwarzane. Najlepszym rozwiązaniem jest przekazanie wszystkich danych dla pliku obiektu blob na raz, a następnie dodać dodatkowe nowsze zdarzenia do innego, nowego pliku obiektu blob.

Przekazywanie bardzo dużej liczby obiektów blob naraz może spowodować, że usługa Stream Analytics pominie czytanie kilku obiektów blob w rzadkich przypadkach. Zaleca się przekazywanie obiektów blob co najmniej 2 sekundy od siebie do magazynu obiektów Blob. Jeśli ta opcja nie jest możliwe, można użyć Centrum zdarzeń do przesyłania strumieniowego dużych ilości zdarzeń. 

### <a name="configure-blob-storage-as-a-stream-input"></a>Konfigurowanie magazynu obiektów Blob jako danych wejściowych strumienia 

W poniższej tabeli wyjaśniono każdą właściwość na stronie **Nowe dane wejściowe** w witrynie Azure portal podczas konfigurowania magazynu obiektów Blob jako danych wejściowych strumienia.

| Właściwość | Opis |
| --- | --- |
| **Alias wejściowy** | Przyjazna nazwa używana w kwerendzie zadania do odwoływania się do tego danych wejściowych. |
| **Subskrypcja** | Wybierz subskrypcję, w której istnieje zasób Usługi IoT Hub. | 
| **Konto magazynu** | Nazwa konta magazynu, na którym znajdują się pliki obiektów blob. |
| **Klucz konta magazynu** | Klucz tajny skojarzony z kontem magazynu. Ta opcja jest automatycznie wypełniana, chyba że wybierzesz opcję ręcznego podania ustawień magazynu obiektów Blob. |
| **Kontener** | Kontener dla danych wejściowych obiektu blob. Kontenery zapewniają logiczne grupowanie obiektów blob przechowywanych w usłudze obiektów Blob platformy Microsoft Azure. Podczas przekazywania obiektu blob do usługi Azure Blob storage, należy określić kontener dla tego obiektu blob. Można wybrać opcję **Użyj istniejącego** kontenera lub **Utwórz nowy,** aby utworzyć nowy kontener.|
| **Wzorzec ścieżki** (opcjonalnie) | Ścieżka pliku używana do lokalizowania obiektów blob w określonym kontenerze. Jeśli chcesz odczytać obiekty BLOB z katalogu głównego kontenera, nie należy ustawiać wzorca ścieżki. W obrębie ścieżki można określić jedno lub więcej wystąpień `{date}` `{time}`następujących trzech zmiennych: , , lub`{partition}`<br/><br/>Przykład 1:`cluster1/logs/{date}/{time}/{partition}`<br/><br/>Przykład 2:`cluster1/logs/{date}`<br/><br/>Znak `*` nie jest dozwoloną wartością prefiksu ścieżki. Dozwolone są tylko <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">prawidłowe znaki obiektów blob platformy Azure.</a> Nie należy dołączać nazw kontenerów ani nazw plików. |
| **Format daty** (opcjonalnie) | Jeśli używasz zmiennej daty w ścieżce, format daty, w którym pliki są zorganizowane. Przykład: `YYYY/MM/DD` |
| **Format czasu** (opcjonalnie) |  Jeśli używasz zmiennej czasowej w ścieżce, format czasu, w którym pliki są zorganizowane. Obecnie jedyną obsługiwaną `HH` wartością jest godzin. |
| **Klucz partycji** | Jeśli dane wejściowe są podzielone na partycje przez właściwość, można dodać nazwę tej właściwości. Klucze partycji są opcjonalne i służy do zwiększenia wydajności kwerendy, jeśli zawiera klauzulę PARTITION BY lub GROUP BY w tej właściwości. |
| **Format serializacji zdarzeń** | Format serializacji (JSON, CSV, Avro lub [Inne (Protobuf, XML, zastrzeżone ...)](custom-deserializer.md)) przychodzącego strumienia danych.  Upewnij się, że format JSON jest zgodny ze specyfikacją i nie zawiera wiodącej 0 dla liczb dziesiętnych. |
| **Kodowanie** | W przypadku csv i JSON UTF-8 jest obecnie jedynym obsługiwanym formatem kodowania. |
| **Kompresja** | Typ kompresji używany do odczytywania przychodzącego strumienia danych, taki jak Brak (domyślny), GZip lub Deflate. |

Gdy dane pochodzą ze źródła magazynu obiektów Blob, masz dostęp do następujących pól metadanych w zapytaniu usługi Stream Analytics:

| Właściwość | Opis |
| --- | --- |
| **Nazwa obiektu Blob** |Nazwa wejściowego obiektu blob, z których pochodzi zdarzenie. |
| **EventProcessedUtcTime** |Data i godzina przetworzenia zdarzenia przez analizę stream analytics. |
| **BlobLastModifiedUtcTime** |Data i godzina ostatniej modyfikacji obiektu blob. |
| **Partitionid** |Identyfikator partycji zerowej dla karty wejściowej. |

Na przykład za pomocą tych pól można napisać kwerendę, podobnie jak w poniższym przykładzie:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
