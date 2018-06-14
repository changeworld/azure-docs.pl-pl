---
title: Telemetrii usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie telemetrii usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 1b26d7925fe5bd39905d9f51d22433b1eea43af6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23861109"
---
# <a name="azure-media-services-telemetry"></a>Telemetrii usługi Azure Media Services

Azure Media Services (AMS) można uzyskać dostępu do swoich usług danych telemetrycznych/metryki. Bieżąca wersja AMS umożliwia zbieranie danych telemetrycznych dla na żywo **kanału**, **StreamingEndpoint**i na żywo **archiwum** jednostek. 

Dane telemetryczne są zapisywane do tabeli magazynu na konto magazynu Azure (zazwyczaj używasz konta magazynu skojarzone z kontem AMS). 

System telemetrii nie zarządza przechowywania danych. Można usunąć stare dane telemetryczne, usuwając tabel do przechowywania.

W tym temacie omówiono sposób konfigurowania i zużywać telemetrii AMS.

## <a name="configuring-telemetry"></a>Konfigurowanie telemetrii

Dane telemetryczne można skonfigurować na poziom szczegółowości na poziomie składnika. Istnieją dwa poziomy szczegółowości "Normal" i "Pełne". Obecnie obu poziomach zwracać tych samych informacji. Zalecane jest użycie "Normalny. 

W następujących tematach opisano sposób włączania telemetrii:

[Włączanie danych telemetrycznych z platformą .NET](media-services-dotnet-telemetry.md) 

[Włączanie telemetrii REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Wykorzystywanie informacji telemetrii

Dane telemetryczne są zapisywane do tabeli magazynu Azure w ramach konta magazynu, które określone podczas konfigurowania dane telemetryczne dla konta usługi Media Services. W tej sekcji opisano magazynu tabel na podstawie metryk.

Dane telemetryczne można korzystać w jednym z następujących sposobów:

- Odczytywanie danych bezpośrednio z magazynem tabel Azure (np. przy użyciu zestawu SDK usługi Magazyn). Opis tabel do przechowywania danych telemetrycznych zawiera **wykorzystywanie informacji telemetrii** w [to](https://msdn.microsoft.com/library/mt742089.aspx) tematu.

Lub

- Użyć funkcji w SDK .NET usługi Media Services do odczytywania danych z magazynu, zgodnie z opisem w [to](media-services-dotnet-telemetry.md) tematu. 


Schemat danych telemetrycznych opisanych poniżej zaprojektowano w celu zapewnić dobrą wydajność w granicach magazynu tabel platformy Azure:

- Konto, identyfikator i identyfikator usługi Zezwalaj na podstawie danych telemetrycznych z każdej usługi można wykonać zapytania niezależnie partycjonowanego danych.
- Partycje zawierają daty umożliwiają uzasadnione górna granica na rozmiar partycji.
- Są klucze komponentu wierszy w kolejności odwrotnej czas do najnowszych elementów danych telemetrycznych można wykonać zapytania dla danej usługi.

Powinno to umożliwić wiele typowych kwerend za skuteczny:

- Równoległe, niezależne pobieranie danych dla poszczególnych usług.
- Pobieranie wszystkich danych dla danej usługi w zakresie daty.
- Pobieranie najnowszych danych dla usługi.

### <a name="telemetry-table-storage-output-schema"></a>Schemat danych wyjściowych magazynu tabeli telemetrii

Dane telemetryczne są przechowywane w agregacji w jednej tabeli, "TelemetryMetrics20160321", gdzie "20160321" jest data utworzonej tabeli. Dane telemetryczne system tworzy osobnej tabeli dla każdego dnia nowy, oparty na 00:00 czasu UTC. Tabela jest używana do przechowywania wartości cykliczne pozyskiwania, takich jak szybkości transmisji bitów w ramach danego okna czasu wysłane bajty, itp. 

Właściwość|Wartość|Przykłady/notes
---|---|---
PartitionKey|{Identyfikator konta} _ {identyfikator jednostki}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66 < br /<br/>Identyfikator konta znajduje się w kluczu partycji w celu uproszczenia przepływy pracy, gdzie wiele kont usługi Media Services będą zapisywane do tego samego konta magazynu.
RowKey|{sekund północy} _ {losowych wartości}|01688_00199<br/><br/>Klucz wiersza rozpoczyna się od liczby sekund północy umożliwiającej dostęp zapytań top n stylu w partycji. Aby uzyskać więcej informacji, zobacz [to](../cosmos-db/table-storage-design-guide.md#log-tail-pattern) artykułu. 
Znacznik czasu|Data i godzina|Automatycznie sygnatura czasowa z tabeli platformy Azure 2016-09-09T22:43:42.241Z
Typ|Typ jednostki, dostarczając danych telemetrii|Kanał/StreamingEndpoint/archiwum<br/><br/>Typ zdarzenia jest tylko wartość ciągu.
Nazwa|Nazwa zdarzenia telemetrii|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|Czas wystąpiło zdarzenie telemetrii (UTC)|2016-09-09T22:42:36.924Z<br/><br/>Czas obserwowanych są udostępniane przez jednostki wysyłania danych telemetrycznych (na przykład kanał). Może to być problemy z synchronizacją między składnikami, dlatego ta wartość jest w przybliżeniu czas
ServiceID|{Identyfikator usługi}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Właściwości jednostki|Zgodnie z definicją zdarzenia|StreamName: stream1, szybkości transmisji bitów 10123...<br/><br/>Pozostałe właściwości są definiowane dla danego typu zdarzenia. Zawartości tabeli platformy Azure jest pary wartości klucza.  (to znaczy różne wiersze w tabeli mają różne zestawy właściwości).

### <a name="entity-specific-schema"></a>Schemat specyficzne dla jednostki

Istnieją trzy typy jednostek danych telemetrycznego wpisów wypychana z następującą częstotliwością:

- Punkty końcowe przesyłania strumieniowego: co 30 sekund
- Na żywo kanałów: co minutę
- Na żywo archiwum: co minutę

**Punkt końcowy przesyłania strumieniowego**

Właściwość|Wartość|Przykłady
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Znacznik czasu|Znacznik czasu|Automatycznie sygnatury czasowej z Azure tabeli 2016-09-09T22:43:42.241Z
Typ|Typ|StreamingEndpoint
Nazwa|Nazwa|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|Identyfikator usługi|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Nazwa hosta|Nazwa hosta punktu końcowego|builddemoserver.Origin.mediaservices.Windows.NET
statusCode|Stan rekordów HTTP|200
resultCode|Szczegóły kod wyniku|S_OK
RequestCount|Całkowita liczba żądań w agregacji|3
Żądania|Wysłane bajty zagregowane|2987358
ServerLatency|Server Średni czas oczekiwania (łącznie z magazynem)|129
E2ELatency|Średnie opóźnienie end-to-end|250

**Kanału na żywo**

Właściwość|Wartość|Przykłady/notes
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Znacznik czasu|Znacznik czasu|Automatycznie sygnatura czasowa z tabeli platformy Azure 2016-09-09T22:43:42.241Z
Typ|Typ|Kanał
Nazwa|Nazwa|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|Identyfikator usługi|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Typ ścieżki nr audio/wideo/tekstu|audio/wideo
TrackName|Nazwy ścieżki|wideo/audio_1
Szybkość transmisji bitów|Śledź szybkości transmisji bitów|785000
Customattributes —||   
IncomingBitrate|Rzeczywista szybkość transmisji bitów przychodzących|784548
OverlapCount|Nakładanie w pozyskiwanie|0
DiscontinuityCount|Brak ciągłości dla ścieżki|0
LastTimestamp|Sygnatura czasowa ostatniego pozyskiwane danych|1800488800
NonincreasingCount|Liczba fragmentów odrzuconych z powodu nierosnąca sygnatury czasowej|2
UnalignedKeyFrames|Gdzie klucza ramek nie jest wyrównana czy odebrano fragment(s) (za pośrednictwem poziomy jakości) |True
UnalignedPresentationTime|Określa, czy odebrano fragment(s) (za pośrednictwem poziomy jakości/ścieżek), gdzie czas prezentacji nie jest wyrównany|True
UnexpectedBitrate|Wartość PRAWDA Jeśli jest obliczana rzeczywista szybkość transmisji bitów audio/wideo śledzenia > 40 000/s i IncomingBitrate == lub IncomingBitrate i actualBitrate różnią się 50% 0 |True
W dobrej kondycji|Wartość true, jeśli <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> są wszystkie 0|True<br/><br/>Dobrej kondycji jest złożonych funkcji, która zwraca wartość false, jeśli zawiera jeden z następujących warunków:<br/><br/>-OverlapCount > 0.<br/>-DiscontinuityCount > 0.<br/>-NonincreasingCount > 0.<br/>-UnalignedKeyFrames == True<br/>-UnalignedPresentationTime == True<br/>-UnexpectedBitrate == True

**Archiwum na żywo**

Właściwość|Wartość|Przykłady/notes
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Znacznik czasu|Znacznik czasu|Automatycznie sygnatura czasowa z tabeli platformy Azure 2016-09-09T22:43:42.241Z
Typ|Typ|Archiwum
Nazwa|Nazwa|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|Identyfikator usługi|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|Adres url programu|asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ISM
TrackName|Nazwy ścieżki|audio_1
TrackType|Typ ścieżki|Audio i wideo
Element CustomAttribute|Liczba szesnastkowa ciąg, który rozróżnia między inną ścieżkę o tej samej nazwie i szybkości transmisji bitów (multi aparatu kąt)|
Szybkość transmisji bitów|Śledź szybkości transmisji bitów|785000
W dobrej kondycji|Wartość true, jeśli FragmentDiscardedCount == 0 & & ArchiveAcquisitionError == False|Wartość true (te dwie wartości nie są obecne w metrykę, ale są one obecne w zdarzeniu źródła)<br/><br/>Dobrej kondycji jest złożonych funkcji, która zwraca wartość false, jeśli zawiera jeden z następujących warunków:<br/><br/>-FragmentDiscardedCount > 0.<br/>-ArchiveAcquisitionError == True

## <a name="general-qa"></a>Ogólne pytań i odpowiedzi

### <a name="how-to-consume-metrics-data"></a>Jak korzystać z danych metryk?

Metryki dane są przechowywane w postaci serii tabel Azure na koncie magazynu klienta. Te dane mogą być używane, za pomocą następujących narzędzi:

- AMS SDK
- Eksplorator usługi Storage Azure firmy Microsoft (obsługuje eksportu w formacie wartości rozdzielanych przecinkami i przetworzonych w programie Excel)
- Interfejs API REST

### <a name="how-to-find-average-bandwidth-consumption"></a>Jak znaleźć średniej przepustowości?

Średniej przepustowości jest średnią żądania w danym okresie czasu.

### <a name="how-to-define-streaming-unit-count"></a>Jak określić liczbę jednostek przesyłania strumieniowego?

Liczba jednostek przesyłania strumieniowego można zdefiniować jako przepływności szczytu z rozdzielonych przepływność szczytu jeden punkt końcowy przesyłania strumieniowego punktów końcowych przesyłania strumieniowego usługi. Szczytowa przepływność można używać jednego punktu końcowego przesyłania strumieniowego to 160 MB/s.
Na przykład załóżmy, że przepływność szczytu z klienta usługi jest 40 MB/s (maksymalna wartość BytesSent w danym okresie czasu). Następnie, liczba jednostek przesyłania strumieniowego jest równa (40 MB/s) * (8 bitów/bajtów) /(160 Mbps) = 2 jednostki przesyłania strumieniowego.

### <a name="how-to-find-average-requestssecond"></a>Jak znaleźć średnią żądań/sekundę?

Aby znaleźć średnią liczbę żądań na sekundę, obliczeń średnia liczba żądań (RequestCount) w danym okresie czasu.

### <a name="how-to-define-channel-health"></a>Jak zdefiniować kanał kondycji?

Kanał kondycji można zdefiniować jako złożone funkcja logiczna taki sposób, że ma wartość false, gdy przytrzymaj dowolny z następujących warunków:

- OverlapCount > 0.
- DiscontinuityCount > 0.
- NonincreasingCount > 0.
- UnalignedKeyFrames == True 
- UnalignedPresentationTime == True 
- UnexpectedBitrate == True


### <a name="how-to-detect-discontinuities"></a>Jak wykryć przerw?

Aby wykrywać przerw, Znajdź wszystkie wpisy danych kanału gdzie DiscontinuityCount > 0. Odpowiednie sygnatury czasowej ObservedTime wskazuje czas, w których wystąpił brak ciągłości.

### <a name="how-to-detect-timestamp-overlaps"></a>Jak wykryć sygnatury czasowej nakłada się na?

Aby wykryć sygnatury czasowej nakładania się, Znajdź wszystkie wpisy danych kanału gdzie OverlapCount > 0. Odpowiednie sygnatury czasowej ObservedTime wskazuje, że wystąpił razy, w których pokrywa się to sygnatura czasowa.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Jak znaleźć przesyłania strumieniowego błędów żądań i przyczyny?

Aby znaleźć przesyłania strumieniowego błędów żądań i przyczyn, Znajdź wszystkie wpisy danych punktu końcowego przesyłania strumieniowego gdzie ResultCode nie jest równa S_OK. Odpowiednie pole StatusCode wskazuje przyczynę błędu żądania.

### <a name="how-to-consume-data-with-external-tools"></a>Jak korzystać z danych za pomocą narzędzi zewnętrznych?

Telemetrycznego danych mogą być przetwarzane i wizualizowane z następujących narzędzi:

- Usługa PowerBI
- Application Insights
- Monitor Azure (dawniej Shoebox)
- Pulpit nawigacyjny na żywo usług AMS
- Portalu Azure (oczekiwanie wersji)

### <a name="how-to-manage-data-retention"></a>Jak zarządzać przechowywania danych?

System telemetrii nie zapewnia zarządzanie przechowywania danych ani automatycznego usuwania starych rekordów. W związku z tym należy do zarządzania i ręcznie usuń stare rekordy z tabeli magazynu. Może się odwoływać do magazynu zestawu SDK dla jak to zrobić.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
