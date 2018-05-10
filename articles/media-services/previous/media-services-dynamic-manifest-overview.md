---
title: Filtry i manifestów dynamiczne | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób tworzenia filtrów dzięki klienta można użyć ich do określonych sekcji strumienia strumienia. Usługa Media Services tworzy dynamiczne manifestów archiwizacji selektywnego strumienia.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/22/2018
ms.author: cenkd;juliako
ms.openlocfilehash: 982af37a866f73292192b0c889e9eeb1e1291030
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="filters-and-dynamic-manifests"></a>Filtry i manifestów dynamiczne
Począwszy od wersji 2.17, Media Services można zdefiniować filtry dla zasobów. Te filtry są reguły po stronie serwera, które umożliwi klientom wybierz można wykonywać następujące czynności: odtwarzanie tylko części klipu wideo (zamiast odtwarzanie całego), lub określ tylko podzestaw wersji audio i wideo, które urządzenia klienta może obsłużyć (zamiast tego wszystkie wersji skojarzonych z elementu zawartości). Filtrowania elementów zawartości zostaną zarchiwizowane za pośrednictwem **dynamiczne manifestu**, które są tworzone na żądanie klienta do strumienia wideo oparte na określonej filtry.

Tematy to omówiono typowe scenariusze, w którym za pomocą filtrów będą bardzo przydatne do klientów i linki do tematów, które przedstawiają sposób tworzyć filtry programowo.

## <a name="overview"></a>Przegląd
Podczas dostarczania zawartości klientom (przesyłanie strumieniowe wydarzeń na żywo lub wideo) poznasz jest dostarczania wideo wysokiej jakości do różnych urządzeń bez względu na warunki panujące w sieci. Aby osiągnąć ten cel, należy wykonać następujące czynności:

* kodowanie strumienia do wielokrotnej szybkości transmisji bitów ([adaptacyjną szybkością transmisji bitów](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) strumienia wideo (to zajmie się jakość i warunki sieciowe) i 
* Użyj usługi Media Services [dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md) do dynamicznie ponownego skompilowania pakietów strumienia do różnych protokołów (to zajmie się przesyłania strumieniowego na różnych urządzeniach). Usługa Media Services obsługuje dostarczanie następujących technologii przesyłania strumieniowego adaptacyjnej szybkości bitowej: HTTP Live Streaming (HLS), Smooth Streaming i MPEG DASH. 

### <a name="manifest-files"></a>Pliki manifestu
Podczas kodowania zawartości do przesyłania strumieniowego o adaptacyjnej szybkości bitowej, **manifestu** (odtwarzania) jest tworzony (ten plik jest tekstowy lub opartych na języku XML). **Manifestu** plik zawiera przesyłania strumieniowego metadane, takie jak: śledzenie typu (audio, wideo lub tekst) oraz śledzenie nazwę, godzina rozpoczęcia i zakończenia, szybkości transmisji bitów (właściwości), Śledź języków, okna prezentacji (czas określony na metodzie przesuwanego okna), (kodera-dekodera wideo FourCC). Zleca player można pobrać fragmentu dalej, podając informacje o następnej rozgrywane fragmentów wideo dostępne i ich lokalizacji. Fragmenty (lub segmentów) są rzeczywiste "fragmentów" zawartości wideo.

Oto przykład pliku manifestu: 

    <?xml version="1.0" encoding="UTF-8"?>    
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

    </SmoothStreamingMedia>

### <a name="dynamic-manifests"></a>Dynamiczne manifestów
Brak [scenariusze](media-services-dynamic-manifest-overview.md#scenarios) gdy klient potrzebuje większą elastyczność niż opisany w zasobów domyślnego pliku manifestu. Na przykład:

* Urządzenie: dostarczać tylko woluminu określonej wersji określonych ścieżek języka, które są obsługiwane przez urządzenia, które jest używane do odtwarzania zawartości ("wersji filtrowanie"). 
* Zmniejsz manifest, aby pokazać klip podrzędne wydarzenia na żywo ("filtrowanie obiekty podrzędne").
* Przytnij początku wideo ("Przycinanie wideo").
* Dostosuj prezentacji okna (DVR) w celu zapewnienia ograniczona długość okna DVR programu Windows Media player ("Dostosowywanie okna prezentacji").

Uzyskanie tego rodzaju elastyczności, Media Services udostępnia **dynamiczne manifesty** na podstawie wstępnie zdefiniowane na [filtry](media-services-dynamic-manifest-overview.md#filters).  Po zdefiniowaniu filtry klientów można ich używać do strumienia określonej wersji lub podrzędne klipy wideo. Czy określają filtry w adresie URL przesyłania strumieniowego. Filtry można zastosować do przesyłania strumieniowego protokołów obsługiwanych przez adaptacyjną szybkością transmisji bitów [dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md): HLS, MPEG DASH i Smooth Streaming. Na przykład:

MPEG DASH URL z filtrem

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

Smooth Streaming URL z filtrem

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Aby uzyskać więcej informacji o tym, jak dostarczać zawartość i kompilacji adresów URL przesyłania strumieniowego, zobacz [dostarczanie zawartości omówienie](media-services-deliver-content-overview.md).

> [!NOTE]
> Należy pamiętać, że manifesty dynamiczne nie należy zmieniać elementu zawartości i manifest domyślny dla tego zasobu. Klienta można zażądać strumienia z lub bez filtrów. 
> 
> 

### <a id="filters"></a>filtry
Istnieją dwa typy filtrów zasobów: 

* Filtry globalne (może odnosić się do żadnych zasobów w ramach konta usługi Azure Media Services, okres istnienia konta) oraz 
* Filtry lokalnego (może być stosowany tylko do zasobów, z którym została powiązana po utworzeniu filtru, okres istnienia elementu zawartości). 

Typy filtru globalne i lokalne mają takie same właściwości. Główna różnica między nimi jest w scenariuszach, które jest bardziej odpowiedni typ plików. Filtry globalne nadają się ogólnie profilów dla urządzeń (filtrowanie wersji) gdzie filtry lokalnego może posłużyć do trim określonych zasobów.

## <a id="scenarios"></a>Typowe scenariusze
Jak wspomniano wcześniej, podczas dostarczania zawartości klientom (przesyłanie strumieniowe wydarzeń na żywo lub wideo) jest dostarczania wideo wysokiej jakości do różnych urządzeń bez względu na warunki panujące w sieci. Ponadto sieci może mają inne wymagania, które obejmują filtrowanie zasobów i korzystanie z **dynamiczne manifestu**s. W poniższych sekcjach znajdują się krótki przegląd informacji o różnych scenariuszy filtrowania.

* Określ podzestaw wersji audio i wideo, które może obsłużyć niektórych urządzeń, (a nie wszystkie wersje, które są skojarzone z elementu zawartości). 
* Odtwarzanie do tyłu tylko części klipu wideo (zamiast odtwarzanie całego).
* Dostosuj DVR okna prezentacji.

## <a name="rendition-filtering"></a>Filtrowanie wersji
Istnieje możliwość kodowania zawartości do wielu profilów kodowania (H.264 linii bazowej, H.264 wysoki, AACL, AACH, Dolby Digital Plus) i wielokrotnych szybkości transmisji bitów jakości. Jednak nie wszystkie urządzenia klienta zostanie obsługują, profile wszystkich elementów zawartości i szybkości transmisji bitów. Starsze urządzenia z systemem Android obsługuje tylko H.264 linii bazowej + AACL. Wysyła wyższej szybkości transmisji bitów do urządzenia, którego nie można uzyskać korzyści, marnuje obliczeń przepustowości i urządzeń. Takie urządzenia muszą dekodowania wszystkie dane informacje tylko do skalowania w dół, do wyświetlenia.

Manifest dynamiczne umożliwia tworzenie profilów urządzeń takich jak telefon komórkowy, konsoli HD/SD itp. i obejmuje ścieżek i właściwości, które mają być częścią każdego profilu.

![Przykład filtrowania wersji][renditions2]

W poniższym przykładzie kodera użytego do kodowania zawartości mezzanine do siedmiu ISO MP4s wersji wideo (od 180p-1080p). Zakodowanym elementem zawartości można dynamicznie umieszczone w dowolnej z następujących protokołów: MPEG DASH, HLS i Smooth.  W górnej części diagramu, jest wyświetlany manifest HLS zasobów bez filtrów (zawiera wszystkie wersje siedem).  W lewym dolnym rogu jest wyświetlana manifest HLS, do którego zastosowano filtr o nazwie "ott". Określa filtr "ott" Aby usunąć wszystkie szybkości transmisji bitów poniżej 1 MB/s, co spowodowało dolnej dwa poziomy jakości trwa odłączany w odpowiedzi.  W prawym dolnym manifest HLS, do którego zastosowano filtr o nazwie "przenośnych" jest wyświetlany. Filtr "przenośnych" Określa usunięcie wersji, gdy rozwiązanie jest większy niż 720p, co spowodowało dwóch wersji 1080p jest odłączony.

![Filtrowanie wersji][renditions1]

## <a name="removing-language-tracks"></a>Usuwanie wersji językowych
Zasobów może zawierać wiele języków audio, takie jak angielski, hiszpański, francuski,... itd. Zwykle menedżerów Player SDK ścieżki audio wybór domyślny i śledzi audio dostępne dla określonego użytkownika. Trudne do opracowywania takich zestawów SDK Player, wymaga różnych implementacji między struktur odtwarzaczy specyficzne dla urządzenia. Ponadto na niektórych platformach interfejsów API Player są ograniczone i nie ma funkcji wyboru audio, których użytkownicy nie mogą wybierz ani zmienić domyślną ścieżkę audio. Filtry zasobów można kontrolować zachowanie, tworząc filtry, które obejmują tylko żądane języki audio.

![Filtrowanie wersji językowych][language_filter]

## <a name="trimming-start-of-an-asset"></a>Przycinanie początkowy środka trwałego
W większości wydarzeń transmisji strumieniowej na żywo Operatorzy uruchomić niektórych testów przed rzeczywistego zdarzenia. Na przykład może obejmować łupków takie przed rozpoczęciem zdarzenia: "Program rozpocznie się na chwilę". Jeśli archiwizacji program testu łupków dane są archiwizowane oraz w prezentacji. Jednak te informacje nie powinny być wyświetlane na klientach. Z dynamicznego manifestu można utworzyć filtr czasu rozpoczęcia i usuwanie niepożądanych danych w manifeście.

![Przycinanie start][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Tworzenie subclips (widoki) z archiwum na żywo
Wiele wydarzeń na żywo są długotrwałe i na żywo archiwum może obejmować wiele zdarzeń. Po zakończeniu wydarzenia na żywo, nadawców można podzielić na żywo archiwum na uruchamianie programu logicznych i zatrzymanie sekwencji. Następnie opublikować te programy wirtualnego oddzielnie bez post przetwarzania archiwum na żywo i nie są tworzone oddzielne zasoby (który nie skorzystać z istniejącej pamięci podręcznej fragmentów w CDN). Przykładami takich programów wirtualnych są kwartałów football lub koszykówki grę, innings w baseball lub pojedynczych zdarzeń programu sportowych.

Z manifestu dynamicznej można tworzyć filtry przy użyciu godziny rozpoczęcia i zakończenia i tworzyć widoki wirtualnego w górnej części archiwum na żywo. 

![Filtr subclip][subclip_filter]

Filtrowane zasobów:

![Nart][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Dostosowywanie okna prezentacji (DVR)
Obecnie usługi Azure Media Services udostępnia archiwum cykliczne, których czas trwania można skonfigurować między 5 minut - 25 godzin. Filtrowanie manifestu może służyć do tworzenia stopniowego okna DVR w górnej części archiwum, bez usuwania nośnika. Istnieje wiele scenariuszy, w którym chcesz zapewnić ograniczone okna DVR przenieść krawędzi na żywo i jednocześnie zachować większy okien archiwizacji nadawców. Nadawca może wystąpić potrzeba użycia danych, która wykracza poza okno DVR, aby wyróżnić klipy lub he\she może być zapewnienie różnych DVR systemu windows dla różnych urządzeń. Na przykład większość urządzeń przenośnych nie obsługują dużych DVR systemu windows (dla klientów pulpitu może mieć okno DVR 2-minutowy dla urządzeń przenośnych i godzinę).

![Okno DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Dopasowywanie LiveBackoff (pozycji na żywo)
Filtrowanie manifestu może służyć do usunięcia kilka sekund od na żywo krawędzi program na żywo. Filtrowanie umożliwia nadawców Obejrzyj prezentację w punkcie publikacji Podgląd i utworzyć anons punkty wstawienia przed widzów odbierania strumienia (kopii — wyłączone przez 30 sekund). Nadawców można wypychania tych anonsów do ich struktur klienta w czasie ich odebrane i przetworzyć informacji przed możliwości anonsu.

Oprócz obsługi anonsu ustawienie LiveBackoff umożliwia dostosowania pozycji przeglądarki tak, kiedy klienci rozchodzenia i trafień na żywo krawędzi fragmenty nadal można uzyskać z serwera zamiast pobierania HTTP 404 lub 412 błędu.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Łączenie wielu reguł w jeden filtr
Można połączyć wiele reguł filtrowania w jeden filtr. Na przykład można zdefiniować regułę"zakresu" do usunięcia typu z archiwum na żywo, a także filtrować dostępne szybkości transmisji bitów. Podczas stosowania wielu reguł filtrowania, wynik końcowy jest przecięcie wszystkie reguły.

![wiele reguł][multiple-rules]

## <a name="create-filters-programmatically"></a>Programowe tworzenie filtrów
Omówiono następujące jednostek usługi Media Services, które są powiązane z filtrów. Artykuł opisuje również sposób programowego tworzenia filtrów.  

[Tworzenie filtrów z interfejsów API REST](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Łączenie wielu filtrów (filtru kompozycji)
Można także połączyć wiele filtrów w jeden adres URL. 

Poniższy scenariusz przedstawiono, dlaczego warto łączyć filtry:

1. Należy filtrować Twojej jakości wideo dla urządzeń przenośnych, takich jak Android i iPAD (w celu ograniczenia jakości wideo). Aby usunąć niechciane właściwości, należy utworzyć filtr globalny odpowiedni w przypadku profilów urządzeń. Jak wspomniano wcześniej w tym artykule, można filtry globalne dla wszystkich zasobów dla tego samego konta usługi media bez żadnych późniejszych skojarzenia. 
2. Należy przyciąć godzina rozpoczęcia i zakończenia środka trwałego. Można to osiągnąć, czy utworzyć filtr lokalnych i ustawić czas rozpoczęcia i zakończenia. 
3. Aby połączyć oba te filtry (bez kombinacja, należy dodać filtr przycinanie, co utrudnia użycie filtru filtrowanie jakości).

Aby połączyć filtrów, należy określić nazwy filtru do manifestu/odtwarzania adres URL z rozdzielanych średnikami. Załóżmy, że masz filtru o nazwie *MyMobileDevice* który filtry właściwości i inny o nazwie *MyStartTime* można ustawić określony czas rozpoczęcia. Można je połączyć następująco:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Maksymalnie trzy filtry można łączyć. 

Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogu.

## <a name="know-issues-and-limitations"></a>Wiedzieć, problemy i ograniczenia
* Dynamiczne manifest działa w GOP granic (klucz ramki), dlatego przycinanie ma GOP dokładności. 
* Można użyć tej samej nazwy filtru dla lokalne i globalne filtry. Należy pamiętać, że filtr lokalny mają wyższy priorytet i zastąpi filtrów globalnych.
* Po zaktualizowaniu filtru może potrwać maksymalnie 2 minuty dla punktu końcowego przesyłania strumieniowego odświeżyć zasady. Jeśli zawartość zostało obsłużone przy użyciu niektóre filtry (i w pamięci podręcznej serwerów proxy i sieci CDN pamięci podręcznych), aktualizowanie tych filtrów może powodować błędy player. Jest zalecane, aby wyczyścić pamięć podręczną po zaktualizowaniu filtru. Jeśli ta opcja nie jest możliwe, należy rozważyć użycie inny filtr.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Dostarczanie zawartości do klientów — omówienie](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
