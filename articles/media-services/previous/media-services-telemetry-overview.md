---
title: Dane telemetryczne usługi Azure Media Services | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie danych telemetrycznych usługi Microsoft Azure Media Services.
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
ms.openlocfilehash: e2cbb36158722a47518f575b391340b5e25bd908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895775"
---
# <a name="azure-media-services-telemetry"></a>Dane telemetryczne usługi Azure Media Services  


> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

Usługa Azure Media Services (AMS) umożliwia dostęp do danych telemetrycznych/metryk dla swoich usług. Bieżąca wersja usługi AMS umożliwia zbieranie danych telemetrycznych dla jednostek **Kanał**na żywo, **StreamingEndpoint**i **archiwum** na żywo. 

Dane telemetryczne są zapisywane w tabeli magazynu na koncie usługi Azure Storage, które określisz (zazwyczaj należy użyć konta magazynu skojarzonego z kontem usługi AMS). 

System telemetrii nie zarządza przechowywaniem danych. Stare dane telemetryczne można usunąć, usuwając tabele magazynu.

W tym temacie omówiono sposób konfigurowania i korzystania z danych telemetrycznych usługi AMS.

## <a name="configuring-telemetry"></a>Konfigurowanie danych telemetrycznych

Można skonfigurować dane telemetryczne na poziomie składnika ziarnistość. Istnieją dwa poziomy szczegółów "Normalny" i "Pełny". Obecnie oba poziomy zwracają te same informacje. Zaleca się stosowanie "Normal. 

W poniższych tematach pokazano, jak włączyć telemetrię:

[Włączanie danych telemetrycznych za pomocą platformy .NET](media-services-dotnet-telemetry.md) 

[Włączanie telemetrii za pomocą restu](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Korzystanie z informacji telemetrycznych

Dane telemetryczne są zapisywane w tabeli usługi Azure Storage na koncie magazynu określonym podczas konfigurowania danych telemetrycznych dla konta usługi Media Services. W tej sekcji opisano tabele magazynu dla metryk.

Dane telemetryczne można wykorzystać w jeden z następujących sposobów:

- Odczytuj dane bezpośrednio z usługi Azure Table Storage (np. przy użyciu SDK magazynu). Aby uzyskać opis tabel magazynu danych telemetrycznych, zobacz **informacje dotyczące danych telemetrycznych w** [tym](https://msdn.microsoft.com/library/mt742089.aspx) temacie.

Lub

- Użyj pomocy technicznej w programie Media Services .NET SDK do odczytywania danych magazynu, zgodnie z [opisem](media-services-dotnet-telemetry.md) w tym temacie. 


Schemat telemetrii opisany poniżej ma na celu zapewnienie dobrej wydajności w granicach usługi Azure Table Storage:

- Dane są podzielone na partycje według identyfikatora konta i identyfikatora usługi, aby umożliwić niezależne zapytanie danych telemetrycznych z każdej usługi.
- Partycje zawierają datę, aby dać rozsądną górną granicę rozmiaru partycji.
- Klucze wierszy są w odwrotnej kolejności czasu, aby umożliwić najnowsze elementy telemetrii, które mają być wyszukiwane dla danej usługi.

Powinno to umożliwić wiele typowych zapytań, aby być skuteczne:

- Równoległe, niezależne pobieranie danych dla oddzielnych usług.
- Pobieranie wszystkich danych dla danej usługi w zakresie dat.
- Pobieranie najnowszych danych dla usługi.

### <a name="telemetry-table-storage-output-schema"></a>Schemat wyjściowy magazynu tabeli telemetrii

Dane telemetryczne są przechowywane w agregacji w jednej tabeli "TelemetryMetrics20160321", gdzie "20160321" jest datą utworzonej tabeli. System telemetrii tworzy oddzielną tabelę dla każdego nowego dnia na podstawie 00:00 UTC. Tabela służy do przechowywania wartości cyklicznych, takich jak łajno bitrate w danym przedziale czasu, bajty wysyłane itp. 

Właściwość|Wartość|Przykłady/notatki
---|---|---
PartitionKey|{identyfikator konta}_{identyfikator jednostki}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>Identyfikator konta jest zawarty w kluczu partycji, aby uprościć przepływy pracy, w których wiele kont usługi Media Services zapisuje się na tym samym koncie magazynu.
RowKey|{sekundy do północy}_{wartość losowa}|01688_00199<br/><br/>Klucz wiersza rozpoczyna się od liczby sekund do północy, aby zezwolić na kwerendy w stylu top n w obrębie partycji. Więcej informacji znajduje się w [tym](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) artykule. 
Znacznik czasu|Data/godzina|Automatyczny znacznik czasu z tabeli platformy Azure 2016-09-09T22:43:42.241Z
Typ|Typ jednostki dostarczającej dane telemetryczne|Kanał/StreamingEndpoint/Archiwum<br/><br/>Typ zdarzenia jest tylko wartością ciągu.
Nazwa|Nazwa zdarzenia telemetryczne|KanałHeartbeat/StreamingEndpointRequestLog
ObservedTime (Czas obserwowany)|Czas wystąpienia zdarzenia telemetrycznego (UTC)|2016-09-09T22:42:36.924Z<br/><br/>Obserwowany czas jest dostarczany przez jednostkę wysyłającą dane telemetryczne (na przykład kanał). Mogą wystąpić problemy z synchronizacją czasu między składnikami, więc ta wartość jest przybliżona
ServiceID (IDA)|{Identyfikator usługi}|f70bd731-691d-41c6-8f2d-671bdc9c7e
Właściwości specyficzne dla encji|Zgodnie z definicją w zdarzeniu|StreamName: stream1, Bitrate 10123, ...<br/><br/>Pozostałe właściwości są zdefiniowane dla danego typu zdarzenia. Zawartość tabeli platformy Azure jest kluczowymi parami wartości.  (oznacza to, że różne wiersze w tabeli mają różne zestawy właściwości).

### <a name="entity-specific-schema"></a>Schemat specyficzny dla jednostki

Istnieją trzy typy wpisów danych telemetrycznych specyficznych dla jednostki, z których każdy jest wypychany z następującą częstotliwością:

- Punkty końcowe przesyłania strumieniowego: co 30 sekund
- Kanały na żywo: Co minutę
- Archiwum na żywo: Co minutę

**Punkt końcowy przesyłania strumieniowego**

Właściwość|Wartość|Przykłady
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Znacznik czasu|Znacznik czasu|Automatyczny znacznik czasu z tabeli platformy Azure 2016-09-09T22:43:42.241Z
Typ|Typ|Punkt przesyłania strumieniowego
Nazwa|Nazwa|StreamingEndpointRequestLog
ObservedTime (Czas obserwowany)|ObservedTime (Czas obserwowany)|2016-09-09T22:42:36.924Z
ServiceID (IDA)|Identyfikator usługi|f70bd731-691d-41c6-8f2d-671bdc9c7e
HostName|Nazwa hosta punktu końcowego|builddemoserver.origin.mediaservices.windows.net
Statuscode|Rejestruje stan HTTP|200
Kod wyników|Szczegóły kodu wyniku|S_OK
Liczba żądań|Całkowita liczba żądań w agregacji|3
Bajtów|Wysłane bajty zagregowane|2987358
ServerLatency (SerwerLatency)|Średnie opóźnienie serwera (łącznie z magazynem)|129
E2ELatency|Średnie opóźnienie typu end-to-end|250

**Kanał na żywo**

Właściwość|Wartość|Przykłady/notatki
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Znacznik czasu|Znacznik czasu|Automatyczny znacznik czasu z tabeli platformy Azure 2016-09-09T22:43:42.241Z
Typ|Typ|Channel
Nazwa|Nazwa|KanałHeartbeat
ObservedTime (Czas obserwowany)|ObservedTime (Czas obserwowany)|2016-09-09T22:42:36.924Z
ServiceID (IDA)|Identyfikator usługi|f70bd731-691d-41c6-8f2d-671bdc9c7e
Typ utworu|Typ ścieżki wideo/audio/tekstu|wideo/audio
Nazwa utworu|Nazwa utworu|wideo/audio_1
Bitrate|Śledź szybkość transmisji bitów|785000
Customattributes||   
PrzychodzącychBitrate|Rzeczywista przychodząca szybkość transmisji bitów|784548
Pokrywanie sięliczą|Nakładanie się w najmocniejszym|0
NieciągłośćCount|Nieciągłość dla toru|0
LastTimestamp (sygnatura ostatniego czasu)|Ostatnio połknięty sygnatura czasowa danych|1800488800
Liczba niewzrostowa|Liczba fragmentów odrzuconych z powodu niewzdrośnienia sygnatury czasowej|2
UnalignedKeyFrames|Czy otrzymaliśmy fragmenty (fragmenty) (na różnych poziomach jakości), w których klatki kluczowe nie są wyrównane |True
UnalignedPresentationTime|Czy otrzymaliśmy fragment (fragmenty) (na różnych poziomach jakości/ścieżkach), w których czas prezentacji nie jest wyrównany|True
Nieoczekiwany Bitrate|To prawda, jeśli obliczona/rzeczywista szybkość transmisji bitów dla ścieżki audio/wideo > 40 000 b/s i IncomingBitrate == 0 OR IncomingBitrate i actualBitrate różnią się o 50% |True
W dobrej kondycji|To prawda, jeśli <br/>nakładające się licz, <br/>NieciągłośćCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>Nieoczekiwany Bitrate<br/> są wszystkie 0|True<br/><br/>Zdrowy jest funkcją złożoną, która zwraca false, gdy którykolwiek z następujących warunków wstrzymania:<br/><br/>- Pokrywaniecount > 0<br/>- NieciągłośćCount > 0<br/>- NonincreasingCount > 0<br/>- UnalignedKeyFrames == Prawda<br/>- UnalignedPresentationTime == Prawda<br/>- UnexpectedBitrate == Prawda

**Archiwum na żywo**

Właściwość|Wartość|Przykłady/notatki
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Znacznik czasu|Znacznik czasu|Automatyczny znacznik czasu z tabeli platformy Azure 2016-09-09T22:43:42.241Z
Typ|Typ|Archiwum
Nazwa|Nazwa|ArchiwumHeartbeat
ObservedTime (Czas obserwowany)|ObservedTime (Czas obserwowany)|2016-09-09T22:42:36.924Z
ServiceID (IDA)|Identyfikator usługi|f70bd731-691d-41c6-8f2d-671bdc9c7e
Nazwa manifestu|Adres URL programu|asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism
Nazwa utworu|Nazwa utworu|audio_1
Typ utworu|Typ utworu|Audio/wideo
Atrybut CustomAttribute|Ciąg sześciokątny, który rozróżnia różne ścieżki o tej samej nazwie i szybkości transmisji bitów (kąt wielu kamer)|
Bitrate|Śledź szybkość transmisji bitów|785000
W dobrej kondycji|True, if FragmentDiscardedCount == 0 && ArchiveAcquisitionError == False|True (te dwie wartości nie są obecne w metryki, ale są one obecne w zdarzeniu źródłowym)<br/><br/>Zdrowy jest funkcją złożoną, która zwraca false, gdy którykolwiek z następujących warunków wstrzymania:<br/><br/>- FragmentDiscardedCount > 0<br/>- ArchiwumAcquisitionError == Prawda

## <a name="general-qa"></a>Ogólne Q&A

### <a name="how-to-consume-metrics-data"></a>Jak korzystać z danych metryk?

Dane metryki są przechowywane jako seria tabel platformy Azure na koncie magazynu klienta. Te dane mogą być używane przy użyciu następujących narzędzi:

- AMS SDK
- Eksplorator magazynu Microsoft Azure (obsługuje eksportowanie do formatu wartości oddzielonych przecinkami i przetwarzane w programie Excel)
- Interfejs API REST

### <a name="how-to-find-average-bandwidth-consumption"></a>Jak znaleźć średnie zużycie przepustowości?

Średnie zużycie przepustowości jest średnią BytesSent w ciągu przedziału czasu.

### <a name="how-to-define-streaming-unit-count"></a>Jak zdefiniować liczbę jednostek przesyłania strumieniowego?

Liczba jednostek przesyłania strumieniowego można zdefiniować jako szczytową przepływność z punktów końcowych przesyłania strumieniowego usługi podzieloną przez szczytową przepływność jednego punktu końcowego przesyłania strumieniowego. Szczytowa przepustowość użytkowa jednego punktu końcowego przesyłania strumieniowego wynosi 160 Mb/s.
Załóżmy na przykład, że szczytowa przepływność z usługi klienta wynosi 40 MB/s (maksymalna wartość BytesSent w okresie czasu). Następnie liczba jednostek przesyłania strumieniowego jest równa (40 MB/s)*(8 bitów/bajt)/(160 Mb/s) = 2 jednostki przesyłania strumieniowego.

### <a name="how-to-find-average-requestssecond"></a>Jak znaleźć średnią liczbę żądań/sekundę?

Aby znaleźć średnią liczbę żądań/sekundę, oblicz średnią liczbę żądań (RequestCount) w okresie czasu.

### <a name="how-to-define-channel-health"></a>Jak zdefiniować kondycję kanału?

Kondycję kanału można zdefiniować jako złożoną funkcję logiczną, tak aby była false, gdy którykolwiek z następujących warunków posiada:

- Pokrywanie liczą > 0
- NieciągłośćLiczność > 0
- NonincreasingCount > 0
- UnalignedKeyFrames == Prawda 
- UnalignedPresentationTime == Prawda 
- UnexpectedBitrate == Prawda


### <a name="how-to-detect-discontinuities"></a>Jak wykryć nieciągłości?

Aby wykryć nieciągłości, znajdź wszystkie wpisy danych kanału, gdzie NieciągłośćCount > 0. Odpowiedni sygnatura czasowa ObservedTime wskazuje czasy, w których wystąpiły nieciągłości.

### <a name="how-to-detect-timestamp-overlaps"></a>Jak wykryć nakładanie sygnatury czasowe?

Aby wykryć nakładanie sygnatury czasowe, znajdź wszystkie wpisy danych kanału, w których > 0. Odpowiedni sygnatura czasowa ObservedTime wskazuje godziny, w których wystąpiło nakładanie sygnatury czasowej.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Jak znaleźć błędy i przyczyny żądania przesyłania strumieniowego?

Aby znaleźć błędy i przyczyny żądania przesyłania strumieniowego, znajdź wszystkie wpisy danych punktu końcowego przesyłania strumieniowego, gdzie kod wynikowy nie jest równy S_OK. Odpowiednie pole StatusCode wskazuje przyczynę niepowodzenia żądania.

### <a name="how-to-consume-data-with-external-tools"></a>Jak korzystać z danych za pomocą narzędzi zewnętrznych?

Dane telemetryczne mogą być przetwarzane i wizualizowane za pomocą następujących narzędzi:

- PowerBI
- Application Insights
- Azure Monitor (dawniej Shoebox)
- Pulpit nawigacyjny usługi AMS na żywo
- Usługa Azure Portal (oczekiwana wersja)

### <a name="how-to-manage-data-retention"></a>Jak zarządzać przechowywaniem danych?

System telemetryczny nie zapewnia zarządzania przechowywaniem danych ani automatycznego usuwania starych rekordów. W związku z tym należy ręcznie zarządzać i usuwać stare rekordy z tabeli magazynu. Aby dowiedzieć się, jak to zrobić, można zapoznać się z sdk magazynu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
