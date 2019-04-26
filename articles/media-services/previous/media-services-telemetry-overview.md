---
title: Telemetria usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie telemetrii usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 8e8b493881662483e66dd835d1cc68a471b18454
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60545524"
---
# <a name="azure-media-services-telemetry"></a>Azure Media Services telemetry  


> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

Usługa Azure Media Services (AMS) można uzyskać dostępu do swoich usług przy użyciu danych telemetrycznych/metryki. Bieżąca wersja usługi AMS umożliwia zbieranie danych telemetrycznych na żywo **kanału**, **StreamingEndpoint**i na żywo **archiwum** jednostek. 

Dane telemetryczne są zapisywane w tabeli magazynu na koncie magazynu platformy Azure, który określisz (zazwyczaj można użyć konta magazynu skojarzone z kontem usługi AMS). 

System telemetrii nie zarządza przechowywania danych. Usuwanie tabel magazynu można usunąć stare dane telemetryczne.

W tym temacie omówiono sposób konfigurowania i korzystania z danych telemetrycznych usługi AMS.

## <a name="configuring-telemetry"></a>Konfigurowanie telemetrii

Telemetrię można skonfigurować na składnika z poziomu szczegółowości. Istnieją dwa poziomy szczegółowości "Normal" i "Pełne". Obecnie obu poziomach zwracają te same informacje. Zalecane jest użycie "Normal. 

W następujących tematach opisano, jak włączyć telemetrię:

[Włączanie telemetrii przy użyciu platformy .NET](media-services-dotnet-telemetry.md) 

[Włączanie telemetrii za pomocą architektury REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Wykorzystywanie informacji telemetrycznych

Dane telemetryczne są zapisywane do tabeli usługi Azure Storage na koncie magazynu, który określiłeś, podczas konfigurowania telemetrii dla konta usługi Media Services. W tej sekcji opisano tabel magazynu dla metryk.

Korzystanie z danych telemetrycznych, w jednym z następujących sposobów:

- Odczytywać dane bezpośrednio z usługi Azure Table Storage (np. przy użyciu zestawu SDK usługi Storage). Opis tabel magazynu danych telemetrycznych, patrz **wykorzystywanie informacje telemetryczne** w [to](https://msdn.microsoft.com/library/mt742089.aspx) tematu.

Lub

- Na użytek obsługi w Media Services .NET SDK odczytu magazynu danych, zgodnie z opisem w [to](media-services-dotnet-telemetry.md) tematu. 


Aby zapewnić dobrą wydajność w ramach usługi Azure Table Storage została zaprojektowana w schematu danych telemetrycznych, opisane poniżej:

- Dane są partycjonowane przez identyfikator konta i identyfikator usługi umożliwia przetwarzanie danych telemetrycznych z każdej usługi niezależne zostać wykonane zapytanie.
- Partycje zawierają datę, aby nadać uzasadnione górną granicę na rozmiar partycji.
- Klucze wiersza są w kolejności odwrotnej czas do najnowszych elementów danych telemetrycznych można wykonywać zapytania dla danej usługi.

Powinno to umożliwić wiele typowych zapytań, aby były skuteczne:

- Równoległe, niezależnie od pobierania danych dla poszczególnych usług.
- Pobieranie wszystkich danych dla danej usługi w zakresie daty.
- Pobieranie najnowszych danych dla usługi.

### <a name="telemetry-table-storage-output-schema"></a>Schemat danych wyjściowych magazynu tabel danych telemetrycznych

Dane telemetryczne są przechowywane w agregacji w jednej tabeli, "TelemetryMetrics20160321" gdzie "20160321" jest data utworzonej tabeli. System telemetrii tworzy tabelę oddzielnych za każdy dzień nowy, oparty o 00:00 czasu UTC. Tabela jest używana do przechowywania wartości cyklicznych takich jak pozyskiwania szybkości transmisji bitów w danym przedziale godzina, liczba wysłanych bajtów, itp. 

Właściwość|Wartość|Przykłady/notes
---|---|---
PartitionKey|{account ID}_{entity ID}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>Identyfikator konta znajduje się w kluczu partycji można uproszczenie przepływów pracy, w których wielu kont usługi Media Services będą zapisywane do tego samego konta magazynu.
RowKey|{seconds północy} _ {losową wartość}|01688_00199<br/><br/>Klucz wiersza rozpoczyna się od liczbę sekund, północy górnego n zapytań w ramach partycji. Więcej informacji znajduje się w [tym](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) artykule. 
Znacznik czasu|Data/godzina|Auto sygnatury czasowej z tabeli platformy Azure 2016-09-09T22:43:42.241Z
Type|Typ jednostki, podając dane telemetryczne|Archiwum/StreamingEndpoint/kanału<br/><br/>Typ zdarzenia jest po prostu wartość ciągu.
Name (Nazwa)|Nazwa zdarzenia telemetrii|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|Czas wystąpienia zdarzenia telemetrii (UTC)|2016-09-09T22:42:36.924Z<br/><br/>Zaobserwowany czasu jest zapewniana przez jednostki wysyłania danych telemetrycznych (na przykład kanał). Może to być razem, gdy problemy z synchronizacją między składnikami, więc ta wartość jest przybliżona
Identyfikator usługi|{Identyfikator usługi}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Właściwości specyficzne dla jednostki|Zgodnie z definicją zdarzenia|StreamName: stream1, szybkości transmisji bitów 10123...<br/><br/>Pozostałe właściwości są zdefiniowane dla danego typu zdarzenia. Zawartość tabel platformy Azure to pary klucz-wartość.  (czyli różne wiersze w tabeli mają różne zestawy właściwości).

### <a name="entity-specific-schema"></a>Schemat specyficzne dla jednostki

Istnieją trzy typy telemetria dane specyficzne dla jednostki wpisów, wypychane z częstotliwością następujące:

- Punkty końcowe przesyłania strumieniowego: Co 30 sekund
- Usługa kanały na żywo: Co minutę
- Dynamiczne archiwum: Co minutę

**Punkt końcowy przesyłania strumieniowego**

Właściwość|Wartość|Przykłady
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Znacznik czasu|Znacznik czasu|Auto sygnaturę czasową od usługi Azure Table 2016-09-09T22:43:42.241Z
Type|Type|StreamingEndpoint
Name (Nazwa)|Name (Nazwa)|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
Identyfikator usługi|Identyfikator usługi|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Nazwa hosta|Nazwa hosta punktu końcowego|builddemoserver.origin.mediaservices.windows.net
StatusCode|Stan rekordów HTTP|200
ResultCode|Szczegóły kodu wyniku|S_OK
RequestCount|Łączna liczba żądań w agregacji|3
BytesSent|Wysłane bajty zagregowanych|2987358
ServerLatency|Opóźnienie serwera średni (w tym magazyn)|129
E2ELatency|Średnie opóźnienie end-to-end|250

**Kanału na żywo**

Właściwość|Wartość|Przykłady/notes
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Znacznik czasu|Znacznik czasu|Auto sygnatury czasowej z tabeli platformy Azure 2016-09-09T22:43:42.241Z
Type|Type|Kanał
Name (Nazwa)|Name (Nazwa)|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
Identyfikator usługi|Identyfikator usługi|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Typ ścieżki wideo/audio/tekstu|audio/wideo
TrackName|Nazwy ścieżki|Film wideo/audio_1
Szybkość transmisji bitów|Śledź szybkości transmisji bitów|785000
Customattributes —||   
IncomingBitrate|Rzeczywista szybkość transmisji bitów przychodzących|784548
OverlapCount|Nakładanie w pozyskiwania|0
DiscontinuityCount|Ciągłości dla ścieżki|0
LastTimestamp|Sygnatura czasowa ostatniego pozyskiwanych danych|1800488800
NonincreasingCount|Liczba fragmentów dla odrzuconych z powodu bez zwiększania znacznik czasu:|2
UnalignedKeyFrames|Czy odebraliśmy fragment(s) (za pośrednictwem poziomy jakości) gdzie nie jest wyrównana klatki kluczowe |True
UnalignedPresentationTime|Czy odebraliśmy fragment(s) (za pośrednictwem poziomy jakości/ścieżek), gdzie czas prezentacji nie jest wyrównany|True
UnexpectedBitrate|Wartość true Jeśli obliczane rzeczywistego transmisji bitów dla audio/wideo śledzenia > 40 000 b/s i IncomingBitrate == 0 lub IncomingBitrate i actualBitrate różnią się o 50% |True
W dobrej kondycji|Wartość true, jeśli <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> to wszystkie 0|True<br/><br/>Dobrej kondycji jest złożone funkcja, która zwraca wartość false, gdy przytrzymać dowolne z następujących warunków:<br/><br/>-OverlapCount > 0<br/>-DiscontinuityCount > 0<br/>-NonincreasingCount > 0<br/>-UnalignedKeyFrames == True<br/>-UnalignedPresentationTime == True<br/>-UnexpectedBitrate == True

**Dynamiczne archiwum**

Właściwość|Wartość|Przykłady/notes
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Znacznik czasu|Znacznik czasu|Auto sygnatury czasowej z tabeli platformy Azure 2016-09-09T22:43:42.241Z
Type|Type|Archiwum
Name (Nazwa)|Name (Nazwa)|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
Identyfikator usługi|Identyfikator usługi|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|Adres url programu|asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism
TrackName|Nazwy ścieżki|audio_1
TrackType|Typ ścieżki|Audio/wideo
CustomAttribute —|Wartość szesnastkową ciąg, który rozróżnia między różnych śledzenie o takiej samej nazwie i szybkości transmisji bitów (kąt multi aparatu fotograficznego)|
Szybkość transmisji bitów|Śledź szybkości transmisji bitów|785000
W dobrej kondycji|Wartość true, jeśli FragmentDiscardedCount == 0 & & ArchiveAcquisitionError == False|Wartość true, (te dwie wartości nie są obecne w metrykę, ale są obecne w zdarzeniu źródła)<br/><br/>Dobrej kondycji jest złożone funkcja, która zwraca wartość false, gdy przytrzymać dowolne z następujących warunków:<br/><br/>-FragmentDiscardedCount > 0<br/>-ArchiveAcquisitionError == True

## <a name="general-qa"></a>Ogólne pytania i odpowiedzi

### <a name="how-to-consume-metrics-data"></a>Jak używać danych metryk?

Dane metryk są przechowywane jako serię tabele platformy Azure na koncie magazynu klienta. Te dane mogą być wykorzystywane za pomocą następujących narzędzi:

- ZESTAW SDK USŁUGI AMS
- Microsoft Azure Storage Explorer (obsługuje eksportowania do formatu wartości rozdzielane przecinkami i przetworzone w programie Excel)
- Interfejs API REST

### <a name="how-to-find-average-bandwidth-consumption"></a>Jak znaleźć średniej przepustowości?

Użycia Średnia przepustowość jest średnia BytesSent w danym okresie czasu.

### <a name="how-to-define-streaming-unit-count"></a>Jak zdefiniować liczby jednostek przesyłania strumieniowego?

Liczba jednostek przesyłania strumieniowego mogą być definiowane jako przepływności szczytu od podzielona przez przepływność szczytu jeden punkt końcowy przesyłania strumieniowego punkty końcowe przesyłania strumieniowego usługi. Szczytowy przepływności można używać jednego punktu końcowego przesyłania strumieniowego jest 160 MB/s.
Na przykład załóżmy, że przepływność szczytu z działem obsługi klienta jest 40 MB/s (maksymalna wartość BytesSent w danym okresie czasu). Następnie liczby jednostek przesyłania strumieniowego jest równa (40 MB/s) * (8 bitów/bajtów) /(160 Mbps) = 2 jednostki przesyłania strumieniowego.

### <a name="how-to-find-average-requestssecond"></a>Jak znaleźć średnia żądań na sekundę?

Aby znaleźć średnią liczbę żądań na sekundę, należy obliczyć średnia liczba żądań (RequestCount) w danym okresie czasu.

### <a name="how-to-define-channel-health"></a>Jak zdefiniować kondycji kanał?

Kanał kondycji mogą być definiowane jako funkcja logiczna złożonego, taki sposób, że ma wartość false, gdy dowolne z następujących warunków przechowywania:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames == True 
- UnalignedPresentationTime == True 
- UnexpectedBitrate == True


### <a name="how-to-detect-discontinuities"></a>Jak wykryć przerw?

Aby wykryć przerw, znaleźć wszystkie wpisy danych kanału gdzie DiscontinuityCount > 0. Odpowiednie sygnatura czasowa ObservedTime wskazuje czas, w których wystąpił brak ciągłości.

### <a name="how-to-detect-timestamp-overlaps"></a>Jak wykrywać nakładania się sygnatura czasowa?

Aby wykrywać nakładania się sygnatura czasowa, znaleźć wszystkie wpisy danych kanału gdzie OverlapCount > 0. Znacznik czasu: ObservedTime odpowiedniego wskazuje, że wystąpił razy, w których nakłada sygnaturę czasową.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Jak znaleźć przesyłania strumieniowego błędów żądań i przyczyny?

Można znaleźć przesyłania strumieniowego błędów żądań i przyczyny, należy znaleźć wszystkie wpisy danych punktu końcowego przesyłania strumieniowego, gdzie kod wyniku nie jest równa S_OK. Odpowiednie pole StatusCode wskazuje przyczynę błędu żądania.

### <a name="how-to-consume-data-with-external-tools"></a>Jak używać danych za pomocą narzędzi zewnętrznych?

Telemetria dane mogą być przetwarzane i wizualizowane za pomocą następujących narzędzi:

- Usługa PowerBI
- Application Insights
- Usługa Azure Monitor (dawniej Shoebox)
- Pulpit nawigacyjny na żywo usług AMS
- Witryna Azure Portal (oczekiwanie na wydanie)

### <a name="how-to-manage-data-retention"></a>Jak zarządzać przechowywania danych?

System telemetrii nie zapewnia zarządzanie przechowywania danych lub automatycznego usuwania starych rekordów. W związku z tym należy zarządzać i ręcznie usuń stare rekordy z tabeli magazynu. Mogą odwoływać się do usługi storage SDK for jak to zrobić.

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
