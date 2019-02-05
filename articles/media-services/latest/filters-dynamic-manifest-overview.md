---
title: Filtry i usługi Azure Media Services o nazwie manifesty dynamiczne | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób tworzenia filtrów, więc klienta można ich używać do określonych sekcji strumienia strumienia. Usługa Media Services tworzy manifestów dynamicznych do archiwizacji, tym selektywne przesyłania strumieniowego.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/24/2019
ms.author: juliako
ms.openlocfilehash: 139f6283c2b59aee53afa3f0dd52e06e2b0eff4c
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695223"
---
# <a name="filters-and-dynamic-manifests"></a>Filtrów i manifestów dynamicznych

Podczas dostarczania zawartości do klientów (przesyłanie strumieniowe wydarzeń na żywo lub wideo na żądanie) klienta może wymagać większej elastyczności niż opisane w pliku manifestu zasobu domyślnego. Usługa Azure Media Services umożliwia definiowanie filtrów kont i zasobów filtry dla zawartości. 

Filtry są reguły po stronie serwera, które umożliwiają klientom wykonywać następujące czynności: 

- Odtwarzanie tylko na część wideo (zamiast odtwarzanie całego). Na przykład:

    - Zmniejsz manifestu, aby pokazać klipu podrzędnego, wydarzenia na żywo ("podklipów filtering"), lub
    - Przytnij początku wideo ("Przycinanie wideo").

- Dostarczaj tylko określonej wersji i/lub ścieżek określonego języka, które są obsługiwane przez urządzenia, które służy do odtwarzania zawartości ("odwzorowanie filtering"). 
- Dostosuj prezentacji okna (DVR) w celu zapewnienia ograniczona długość okna funkcji DVR w odtwarzaczu ("Dostosowywanie prezentacji okno").

W tym temacie opisano [pojęcia](#concepts) i [pokazuje filtrów definicje](#definitions). Udostępnia szczegółowe informacje o typowych scenariuszach. Na końcu tego artykułu możesz znaleźć linki, które pokazują, jak programowo utworzyć filtry.  

## <a name="concepts"></a>Pojęcia

### <a name="dynamic-manifests"></a>O nazwie manifesty dynamiczne

Usługa Media Services oferuje **manifestów dynamicznych** oparte na wstępnie zdefiniowane [filtry](#filters). Po zdefiniowaniu filtrów, klientów można ich używać do strumienia określonej wersji lub podrzędnych klipów wideo. Filtry mogą określić w adresie URL przesyłania strumieniowego. Filtry można zastosować do adaptacyjną szybkością transmisji bitów, protokołów przesyłania strumieniowego: Apple HTTP Live przesyłania strumieniowego (HLS), między innymi MPEG-DASH i Smooth Streaming. 

W poniższej tabeli przedstawiono przykładowe adresy URL przy użyciu filtrów:

|Protokół|Przykład|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

> [!NOTE]
> Nie zmieniaj manifestów dynamicznych, elementu zawartości i domyślny manifest dla tego zasobu. Klienta można zażądać strumienia z lub bez filtrów. 
> 
> 

### <a name="manifest-files"></a>Pliki manifestu

Podczas kodowania zasobów do przesyłania strumieniowego o adaptacyjnej szybkości transmisji bitów, **manifestu** (listy odtwarzania), plik zostanie utworzony (pliku jest tekstowa lub opartych na języku XML). **Manifestu** plik zawiera przesyłania strumieniowego metadane, takie jak: śledzenie typu (audio, wideo lub tekst), śledzenie, name, godzina rozpoczęcia i zakończenia, szybkości transmisji bitów (właściwości), Śledź języków, okna prezentacji (przesuwającego się okna o określonej długości) i kodera-dekodera wideo ( FourCC). Informuje również gracza, aby pobrać następny fragment, podając informacje o następnej rozgrywane fragmenty wideo dostępne i ich lokalizacji. Fragmenty (lub segmentów) są rzeczywiste "fragmentów" zawartości wideo.

Oto przykład HLS pliku manifestu: 

```
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(380658)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(380658)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721426)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721426)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1155246)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1155246)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2218559)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2218559)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579378)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579378)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="get-and-examine-manifest-files"></a>Pobierz i sprawdź plik manifestu

Należy określić listę warunków właściwość śledzenie filtru podstawie, na którym ścieżki strumienia (na żywo lub wideo na żądanie) powinny zostać uwzględnione w manifeście utworzony dynamicznie. Aby uzyskać i zbadać właściwości ścieżki, musisz najpierw załadować manifestu Smooth Streaming.

[Przekazywanie, kodowanie i przesyłanie strumieniowe plików przy użyciu platformy .NET](stream-files-tutorial-with-api.md) samouczka dowiesz się, jak tworzyć adresy URL przesyłania strumieniowego przy użyciu platformy .NET (znajduje się pozycja [tworzenie adresów URL](stream-files-tutorial-with-api.md#get-streaming-urls) sekcji). Po uruchomieniu aplikacji, wskazuje jeden z adresów URL w manifeście Smooth Streaming: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/>Skopiuj i wklej adres URL w pasku adresu przeglądarki. Plik zostanie pobrany. Możesz otworzyć go w wybranym edytorze tekstów.

Na przykład REST, zobacz [przekazywanie, kodowanie i przesyłanie strumieniowe plików z użyciem usług REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitor szybkości transmisji bitów strumienia wideo

Możesz użyć [strony pokaz usługi Azure Media Player](http://aka.ms/amp) monitorowanie szybkości transmisji bitów strumienia wideo. Na stronie wersji demonstracyjnej są wyświetlane informacje diagnostyczne w **diagnostyki** karty:

![Diagnostyka Azure Media Player][amp_diagnostics]

## <a name="defining-filters"></a>Definiowanie filtrów

Istnieją dwa typy filtrów zasobów: 

* [Konto filtry](https://docs.microsoft.com/rest/api/media/accountfilters) (globalna) — można zastosować do dowolnego zasobu w ramach konta usługi Azure Media Services, okres istnienia konta.
* [Filtry zasobów](https://docs.microsoft.com/rest/api/media/assetfilters) (local) — można stosować tylko do elementu zawartości, z którym filtr został skojarzony po utworzeniu, okres istnienia zasobu. 

[Konto filtru](https://docs.microsoft.com/rest/api/media/accountfilters) i [filtr zasobów](https://docs.microsoft.com/rest/api/media/assetfilters) typy mają te same właściwości, definiując/opisu filtru. Podczas tworzenia, z wyjątkiem **filtr zasobów**, należy określić nazwę zasobu za pomocą którego chcesz skojarzyć z filtrem.

W zależności od scenariusza możesz zdecydować, jakiego rodzaju filtru jest bardziej odpowiednie (filtr zasobu lub Filtr konta). Filtry kont są odpowiednie dla profilów urządzeń (odwzorowanie filtrowanie) gdzie filtry zasobów może służyć można przycięcia do określonego zasobu.

Możesz użyć następujących właściwości do opisania filtry. 

|Name (Nazwa)|Opis|
|---|---|
|firstQuality|Pierwszy bitrate jakości filtru.|
|presentationTimeRange|Zakres czasu prezentacji. Ta właściwość jest używana do filtrowania punktów manifestu rozpoczęcia/zakończenia, długość okna prezentacji i pozycja początkowa na żywo. <br/>Aby uzyskać więcej informacji, zobacz [PresentationTimeRange](#PresentationTimeRange).|
|ścieżki|Warunki wybór ścieżki. Aby uzyskać więcej informacji, zobacz [ścieżki](#tracks)|

### <a name="presentationtimerange"></a>PresentationTimeRange

Użyj tej właściwości, za pomocą **filtry zasobów**. Nie zaleca się ustawienie właściwości z **filtrów kont**.

|Name (Nazwa)|Opis|
|---|---|
|**endTimestamp**|Przedział czasu zakończenia bezwzględne. Ma zastosowanie do materiałów wideo na żądanie (VoD). Prezentacji na żywo jest dyskretnie ignorowana i stosowane po zakończeniu prezentacji i dane ze strumienia staje się wideo na żądanie.<br/><br/>Wartość reprezentuje punkt końcowy bezwzględne strumienia. Pobiera on zaokrąglony do najbliższej następnym uruchomieniu GOP.<br/><br/>Użyj StartTimestamp i EndTimestamp można przycięcia listy odtwarzania (manifest). Na przykład StartTimestamp = 40000000 i EndTimestamp = 100000000 wygeneruje listy odtwarzania, która zawiera multimediów między StartTimestamp i EndTimestamp. Jeśli fragment pokrywającej granicy, całego fragmentu będą uwzględniane w manifeście.<br/><br/>Zobacz też **forceEndTimestamp** definicję, która jest zgodna.|
|**forceEndTimestamp**|Dotyczy filtrów na żywo.<br/><br/>**forceEndTimestamp** jest wartością logiczną, wskazującą, czy **endTimestamp** zostało ustawione na prawidłową wartość. <br/><br/>Jeśli wartość jest **true**, **endTimestamp** wartość powinna być określona. Jeśli nie zostanie określony, zwracany jest nieprawidłowe żądanie.<br/><br/>Jeśli na przykład, aby zdefiniować filtr, który rozpoczyna się od 5 minut do wejściowego pliku wideo, a jest dostępna do końca strumienia, należy ustawić **forceEndTimestamp** o wartości false i pominąć ustawienie **endTimestamp**.|
|**liveBackoffDuration**|Dotyczy tylko na żywo. Właściwość jest używana do definiowania pozycji odtwarzania na żywo. Przy użyciu tej reguły, możesz opóźnić pozycji odtwarzania na żywo i utworzyć bufor po stronie serwera dla graczy. LiveBackoffDuration jest określana względem transmisji na żywo. Czas trwania maksymalna wycofywania na żywo to 300 sekund.|
|**presentationWindowDuration**|Ma zastosowanie do na żywo. Użyj **presentationWindowDuration** dotyczą przesuwającego się okna listy odtwarzania. Na przykład ustawić presentationWindowDuration = 1200000000, aby zastosować przesuwającego się okna dwie minuty. Nośnik w ciągu 2 minut na żywo edge zostaną uwzględnione w listy odtwarzania. Jeśli fragment pokrywającej granicy, całego fragmentu zostaną uwzględnione na liście odtwarzania. Czas trwania okna prezentacji minimalna wynosi 60 sekund.|
|**startTimestamp**|Ma zastosowanie do strumieni wideo na żądanie lub na żywo. Wartość stanowi punkt początkowy bezwzględne strumienia. Wartość jest zaokrąglana do najbliższej następnym uruchomieniu GOP.<br/><br/>Użyj **startTimestamp** i **endTimestamp** można przycięcia listy odtwarzania (manifest). Na przykład startTimestamp = 40000000 i endTimestamp = 100000000 wygeneruje listy odtwarzania, która zawiera multimediów między StartTimestamp i EndTimestamp. Jeśli fragment pokrywającej granicy, całego fragmentu będą uwzględniane w manifeście.|
|**Skala czasu**|Ma zastosowanie do strumieni wideo na żądanie lub na żywo. Skala czasu używany przez sygnatury czasowe i czasów trwania określonym powyżej. Skala czasu domyślny to 10000000. Służy alternatywne skali czasu. Wartość domyślna to 10000000 SNS (kilkuset nanosekund).|

### <a name="tracks"></a>ścieżki

Należy określić listę warunków właściwość śledzenie filtru (FilterTrackPropertyConditions) podstawie, na którym ścieżki strumienia (na żywo lub wideo na żądanie) powinny zostać uwzględnione w manifeście utworzony dynamicznie. Filtry są połączone przy użyciu logicznych **i** i **lub** operacji.

Warunki właściwości śledzenie filtru opisano typy ścieżek, wartości (opisanych w poniższej tabeli) i operacje (równe, NotEqual). 

|Name (Nazwa)|Opis|
|---|---|
|**Szybkość transmisji bitów**|Szybkość transmisji bitów toru używana w celu filtrowania.<br/><br/>Zalecana wartość to zakresu szybkości transmisji w bitach na sekundę. Na przykład, "0-2427000".<br/><br/>Uwaga: podczas korzystania z wartością o określonej szybkości transmisji bitów, takich jak 250000 (liczba bitów na sekundę), to podejście nie jest zalecane, ponieważ dokładne szybkości transmisji mogą się zmieniać z jednego zasobu do innego.|
|**FourCC**|Użyj wartości FourCC ścieżki do filtrowania.<br/><br/>Wartość pierwszego elementu formatu koderów-dekoderów, jak to określono w [RFC 6381](https://tools.ietf.org/html/rfc6381). Obecnie obsługiwane są następujące kodery-dekodery: <br/>Wideo:: "avc1", "hev1", "hvc1"<br/>Audio: "Mp4a" (".", "WE 3"<br/><br/>Można określić wartości FourCC dla ścieżek w zasobie [get i zapoznaj się z plikiem manifestu](#get-and-examine-manifest-files).|
|**Język**|Użyj języka ścieżki do filtrowania.<br/><br/>Wartość tagu języka, który chcesz dołączyć, jak określono w dokumencie RFC 5646. Na przykład "en".|
|**Nazwa**|Użyj nazwy ścieżki w celu filtrowania.|
|**Typ**|Użyj typu ścieżki do filtrowania.<br/><br/>Dozwolone są następujące wartości: "wideo", "audio" lub "text".|

### <a name="example"></a>Przykład

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="rendition-filtering"></a>Filtrowania wyświetlania

Istnieje możliwość kodowanie elementów zawartości do wielu profilów kodowania (H.264 linii bazowej, H.264 wysoki, AACL, AACH, Dolby Digital Plus) i wielokrotnych jakości. Jednak nie wszystkie urządzenia klienta będzie obsługiwać profilów wszystkich elementów zawartości i szybkości transmisji. Starsze urządzenia z systemem Android obsługują tylko H.264 linii bazowej + AACL. Wysyłanie wyższe szybkości transmisji do urządzenia, które nie może uzyskać korzyści, marnuje przepustowości i obliczanie urządzenia. Takie urządzenia muszą dekodowania wszystkie dane informacje tylko do skalowania w dół do wyświetlenia.

Manifestów dynamicznych, umożliwia tworzenie profilów urządzeń takich jak aplikacje mobilne, konsoli HD/SD itp. i obejmuje ścieżek i klas, które mają być częścią każdego profilu.

![Odwzorowanie filtrowanie przykład][renditions2]

W poniższym przykładzie koder użytego do kodowania zasobów mezzanine do siedmiu odwzorowaniami wideo każdego pliku MP4 z ISO (z 180p, aby 1080p). Zakodowanym elementem zawartości można dynamicznie spakowane do żadnego z następujących protokołów: HLS, MPEG DASH i Smooth.  W górnej części diagramu są wyświetlane manifest HLS dla zasobu bez filtrów (zawiera wszystkie wersje siedem).  W lewym dolnym rogu manifest HLS, do którego zastosowano filtr o nazwie "ott" jest wyświetlana. Filtr "ott" Określa, aby usunąć wszystkie różnych poniżej 1 MB/s, co spowodowało dolnej dwa poziomy jakości jest usunięta, a w odpowiedzi. W prawym dolnym rogu manifest HLS, do którego zastosowano filtr o nazwie "mobilnymi" jest wyświetlany. Filtr "przenośnych" Określa usunięcie wersji, gdy rozwiązanie jest większy niż 720p, co spowodowało dwóch wersji 1080p jest odłączony.

![Filtrowania wyświetlania][renditions1]

## <a name="removing-language-tracks"></a>Usunięcie wersji językowych
Twoje zasoby mogą obejmować wiele języków audio, takich jak angielski, hiszpański, francuski, itp. Zazwyczaj menedżerów Player SDK domyślny wybór ścieżki audio i dostępne audio śledzi na wybór użytkownika. Trudne do tworzenia tych zestawów SDK odtwarzacza, wymaga on różne implementacje dla architektury odtwarzaczy specyficznych dla urządzenia. Ponadto na niektórych platformach Player interfejsy API są ograniczone i nie obejmują funkcji wyboru audio, gdzie użytkownicy nie mogą wybrać ani zmienić domyślną ścieżkę audio. Za pomocą filtrów zawartości można kontrolować zachowanie, tworząc filtry, które są uwzględnione jedynie żądane języki audio.

![Język ścieżki filtrowania][language_filter]

## <a name="trimming-start-of-an-asset"></a>Przycinanie początek elementu zawartości
W większości wydarzeń transmisji strumieniowej na żywo Operatorzy uruchomić niektóre testy przed rzeczywistego zdarzenia. Na przykład może być obejmują one plansz następująco przed rozpoczęciem zdarzenia: "Program rozpocznie chwilowo". Jeśli program jest archiwizacji, testów i planszy danych również są archiwizowane i w prezentacji. Jednak te informacje nie być wyświetlane na klientach. Za pomocą manifestów dynamicznych możesz utworzyć filtr godziny rozpoczęcia i usuwanie niepożądanych danych z manifestu.

![Przycinanie start][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Tworzenie subclips (widoki) z archiwum na żywo
Wiele wydarzeń na żywo są długo działające i archiwum na żywo może zawierać wiele zdarzeń. Po zakończeniu wydarzenia na żywo, zastosowań studyjnych warto podzielić dynamiczne archiwum na uruchamianie programu logicznych i Zatrzymaj sekwencji. Następnie publikować te programy wirtualnego oddzielnie bez wpis przetwarzania na żywo archiwum i nie są tworzone oddzielne zasoby (co nie możesz korzystać z istniejącej pamięci podręcznej fragmentów w sieci CDN). Przykładami takich wirtualnych programów są kwartałów football lub przeciwna gry, innings w mecz lub poszczególne zdarzenia programu sportu.

Za pomocą manifestów dynamicznych możesz tworzenie filtrów za pomocą godzin rozpoczęcia/zakończenia i tworzyć widoki wirtualnego w górnej części archiwum na żywo. 

![Filtr klipu podrzędnego][subclip_filter]

Filtrowane zasobów:

![Nartach][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Dostosowywanie okna prezentacji (DVR)
Obecnie usługa Azure Media Services oferuje cykliczne archiwum, w którym można skonfigurować czas trwania między 5 minut — 25 godzin. Filtrowanie manifestu można utworzyć stopniowe okna funkcji DVR w górnej części archiwum, bez usuwania nośnika. Istnieje wiele scenariuszy, w którym chcesz udostępnić ograniczone okno DVR, aby przenieść do krawędzi na żywo i jednocześnie zachować większe okna archiwizacji nadawców. Nadawca może wystąpić potrzeba użycia danych znajdujących się poza okna DVR, aby wyróżnić klipy lub chcą zapewniają różne DVR systemu windows na różnych urządzeniach. Na przykład większość urządzeń przenośnych nie obsługują duże okna DVR (użytkownik ma 2-minutowy okno DVR dla urządzeń przenośnych i godzinę dla klientów komputerów stacjonarnych).

![Okno DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Dostosowywanie LiveBackoff (transmisji na żywo)
Filtrowanie manifestu może służyć do usuwania kilka sekund na żywo krawędzi program na żywo. Filtrowanie dopuszcza nadawców, Obejrzyj prezentację na punkcie publikacji (wersja zapoznawcza) i utworzyć anons wstawiania punkty zanim widzów odbierać strumienia (kopia zatwierdzenia przez 30 sekund). Zastosowań studyjnych można wypchnąć te anonse do ich struktury klienta w czasie dla nich odebranych i przetwarzania informacji przed możliwości anonsu.

Oprócz obsługi anonsu ustawienie LiveBackoff może służyć do dostosowania pozycji osoby przeglądające tak, aby gdy klienci odstępstw i trafień na żywo edge mogą nadal uzyskiwać fragmentów z serwera zamiast 412 Błąd lub błąd HTTP 404.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Łączenie wielu reguł w jeden filtr
Można połączyć wiele reguł filtrowania w jeden filtr. Na przykład można zdefiniować "reguła zakresu" Aby usunąć plansz z archiwum na żywo i również odfiltrować różnych dostępnych. Podczas stosowania wielu reguł filtrowania, efekt jest wspólną wszystkich reguł.

![wiele reguł][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Łączenie wielu filtrów (skład filtru)

Można także połączyć wiele filtrów w pojedynczego adresu URL. 

Następujący scenariusz pokazuje, dlaczego warto połączyć filtrów:

1. Należy filtrować swoje jakość wideo dla urządzeń przenośnych, takich jak Android i urządzenie iPAD (w celu ograniczenia jakość wideo). Aby usunąć jakościami niepożądane, należy utworzyć filtr konta odpowiednie dla profilów urządzeń. Filtry kont może służyć do wszystkich zasobów w ramach tego samego konta usługi media services bez dalszej skojarzenia. 
2. Chcesz także trim godzina rozpoczęcia i zakończenia elementu zawartości. Aby to osiągnąć, czy utworzyć filtr zasobów i ustawić czas rozpoczęcia i zakończenia. 
3. Aby połączyć oba te filtry (bez kombinacji, należy dodać filtrowanie jakości, aby filtrować przycinania, co sprawia, że użycie filtru jest trudniejsze).

Aby połączyć filtry, musisz ustawić nazw filtrów do manifestu/odtwarzania adres URL z rozdzielaną średnikami. Załóżmy, że masz filtr o nazwie *MyMobileDevice* która filtruje jakość i inny o nazwie *MyStartTime* można ustawić określony czas rozpoczęcia. Można połączyć je w następujący sposób:

Można połączyć maksymalnie trzy filtry. 

Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogu.

## <a name="considerations-and-limitations"></a>Istotne zagadnienia i ograniczenia

- Wartości **forceEndTimestamp**, **presentationWindowDuration**, i **liveBackoffDuration** nie powinna być ustawiona dla filtru wideo na żądanie. Są one używane tylko w scenariuszach filtru na żywo. 
- Dynamiczne manifest działa w GOP granic (klucz ramki), dlatego przycinania ma GOP dokładności. 
- Można użyć tej samej nazwy filtru filtry kont i zasobów. Filtry zasobów mają wyższy priorytet i spowoduje przesłonięcie filtrów kont.
- Jeśli zaktualizujesz filtru, może potrwać do 2 minut, zanim punkt końcowy przesyłania strumieniowego odświeżyć zasady. Zawartość zostało obsłużone za pomocą niektóre filtry (i w pamięci podręcznej serwerów proxy i Azure CDN pamięci podręcznych), aktualizowanie te filtry może spowodować błędy odtwarzacza. Zalecane jest, aby wyczyścić pamięć podręczną po zaktualizowaniu filtru. Jeśli ta opcja nie jest możliwe, należy wziąć pod uwagę przy użyciu innego filtru.
- Klienci musieli ręcznie Pobierz manifest i przeanalizować startTimestamp dokładne i skali czasu.
    
    - Aby określić właściwości ścieżki w zasobie [get i zapoznaj się z plikiem manifestu](#get-and-examine-manifest-files).
    - Formuła, aby ustawić filtr zasobu właściwości sygnatury czasowej: <br/>startTimestamp = &lt;godzina rozpoczęcia w manifeście&gt; +  &lt;oczekiwany czas rozpoczęcia filtrowania w ciągu kilku sekund&gt;* Skala czasu


## <a name="next-steps"></a>Kolejne kroki

Następujące artykuły pokazują, jak programowo utworzyć filtry.  

- [Tworzenie filtrów za pomocą interfejsów API REST](filters-dynamic-manifest-rest-howto.md)
- [Tworzenie filtrów za pomocą programu .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Tworzenie filtrów za pomocą interfejsu wiersza polecenia](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
