---
title: Filtrów i manifestów dynamicznych | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób tworzenia filtrów, więc klienta można ich używać do określonych sekcji strumienia strumienia. Usługa Media Services tworzy manifestów dynamicznych do archiwizacji, tym selektywne przesyłania strumieniowego.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: cenkd;juliako
ms.openlocfilehash: 68eeb40e905d089601208d9fc181042c7b434843
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956800"
---
# <a name="filters-and-dynamic-manifests"></a>Filtrów i manifestów dynamicznych

> [!div class="op_single_selector" title1="Wybierz wersję usługi Media Services, którego używasz:"]
> * [Wersja 2](media-services-dynamic-manifest-overview.md)
> * [Wersja 3](../latest/filters-dynamic-manifest-overview.md)

Począwszy od wersji 2.17, Media Services umożliwia definiowanie filtrów dla zasobów. Te filtry są reguły po stronie serwera, które umożliwią klientom chce wykonywać następujące czynności: odtwarzanie tylko na część wideo (zamiast odtwarzanie całego) lub określ tylko podzbiór odwzorowaniami audio i wideo, że urządzenia klienta może obsłużyć () zamiast wszystkich wersji, są skojarzone z elementem zawartości). Filtrowanie zasobów odbywa się za pośrednictwem **manifestów dynamicznych**s, które są tworzone na żądanie klienta do przesyłania strumieniowego wideo oparte na określonej filtry.

W tym temacie omówiono typowe scenariusze, w którym przy użyciu filtrów może być korzystne klientom oraz łącza do tematów, które pokazują, jak programowo utworzyć filtry.

## <a name="overview"></a>Omówienie
Podczas dostarczania zawartości do klientów (przesyłanie strumieniowe wydarzeń na żywo lub wideo na żądanie) celem użytkownika jest dostarczanie wideo o wysokiej jakości do różnych urządzeń warunki panujące w sieci. Aby osiągnąć ten cel, wykonaj następujące czynności:

* kodowanie strumienia do różnych szybkościach transmisji bitów ([adaptacyjną szybkością transmisji bitów](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) strumienia wideo (to zajmie się jakość i warunki sieciowe) i 
* zastosowania usług Media Services [funkcję dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md) do dynamicznego ponownego skompilowania pakietów strumienia do różnych protokołów (ten proces obsłuży przesyłania strumieniowego na różnych urządzeniach). Usługa Media Services obsługuje dostarczanie następujących technologii przesyłania strumieniowego adaptacyjną szybkością transmisji bitów: HTTP Live Streaming (HLS), Smooth Streaming i MPEG DASH. 

### <a name="manifest-files"></a>Pliki manifestu
Podczas kodowania zasobów do przesyłania strumieniowego o adaptacyjnej szybkości transmisji bitów, **manifestu** (listy odtwarzania), plik zostanie utworzony (pliku jest tekstowa lub opartych na języku XML). **Manifestu** plik zawiera przesyłania strumieniowego metadane, takie jak: śledzenie typu (audio, wideo lub tekst), śledzenie, name, godzina rozpoczęcia i zakończenia, szybkości transmisji bitów (właściwości), Śledź języków, okna prezentacji (przesuwającego się okna o określonej długości) i kodera-dekodera wideo ( FourCC). Informuje również gracza, aby pobrać następny fragment, podając informacje o następnej rozgrywane fragmenty wideo dostępne i ich lokalizacji. Fragmenty (lub segmentów) są rzeczywiste "fragmentów" zawartości wideo.

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

### <a name="dynamic-manifests"></a>Manifesty dynamiczne
Istnieją [scenariuszy](media-services-dynamic-manifest-overview.md#scenarios) kiedy klient wymaga większej elastyczności niż opisane w pliku manifestu zasobu domyślnego. Na przykład:

* Określone urządzenie: dostarczanie tylko określonej wersji i/lub ścieżek określonego języka, które są obsługiwane przez urządzenia, które służy do odtwarzania zawartości ("odwzorowanie filtering"). 
* Ogranicz manifestu do wyświetlenia klipów podrzędnych wydarzenia na żywo ("podklipów filtering").
* Przytnij początku wideo ("Przycinanie wideo").
* Dostosuj prezentacji okna (DVR) w celu zapewnienia ograniczona długość okna funkcji DVR w odtwarzaczu ("Dostosowywanie prezentacji okno").

Do osiągnięcia tej elastyczności, Media Services oferuje **manifestów dynamicznych** oparte na wstępnie zdefiniowane [filtry](media-services-dynamic-manifest-overview.md#filters).  Po zdefiniowaniu filtry, klientów można ich używać do strumienia określonej wersji lub podrzędnych klipów wideo. Filtry mogą określić w adresie URL przesyłania strumieniowego. Filtry można zastosować do przesyłania strumieniowego protokołów obsługiwanych przez adaptacyjną szybkością transmisji bitów [funkcję dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md): HLS, MPEG-DASH i Smooth Streaming. Na przykład:

MPEG DASH URL z filtrem

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

Smooth Streaming adres URL z filtrem

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Aby uzyskać więcej informacji o tym, jak dostarczać zawartość i kompilacji adresów URL przesyłania strumieniowego, zobacz [omówienie dostarczania zawartości](media-services-deliver-content-overview.md).

> [!NOTE]
> Należy pamiętać, że manifestów dynamicznych nie zmieniaj elementu zawartości i domyślny manifest dla tego zasobu. Klienta można zażądać strumienia z lub bez filtrów. 
> 
> 

### <a id="filters"></a>Filtry
Istnieją dwa typy filtrów zasobów: 

* Filtry globalne (mogą być stosowane do dowolnego zasobu w ramach konta usługi Azure Media Services, okres istnienia konta) i 
* Filtry lokalnego (można stosować tylko do elementu zawartości, z którym filtr został skojarzony po utworzeniu, okres istnienia elementu zawartości). 

Filtr globalny i lokalny typy mają te same właściwości. Główną różnicą między tymi dwoma jest dla scenariuszy, do których jest bardziej odpowiednia jakiego rodzaju filtr. Filtry globalne nadają się zwykle profilów dla urządzeń (odwzorowanie filtrowanie) gdzie filtry lokalnego może służyć można przycięcia do określonego zasobu.

## <a id="scenarios"></a>Typowe scenariusze
Jak wspomniano, zanim podczas dostarczania zawartości do klientów (przesyłanie strumieniowe wydarzeń na żywo lub wideo na żądanie) dowiesz się, jak dostarczać wideo o wysokiej jakości do różnych urządzeń warunki panujące w sieci. Ponadto może mieć inne wymagania, które obejmują filtrowania zawartości i korzystanie z **manifestów dynamicznych**s. W poniższych sekcjach znajdują się krótki przegląd informacji o różnych scenariuszy filtrowania.

* Określ tylko podzbiór odwzorowaniami audio i wideo, obsługujące przez niektórych urządzeń (zamiast wszystkie wersje, które są skojarzone z elementem zawartości). 
* Odtwarzanie tylko na część wideo (zamiast odtwarzanie całego).
* Dostosuj funkcję DVR z okna prezentacji.

## <a name="rendition-filtering"></a>Filtrowania wyświetlania
Istnieje możliwość kodowanie elementów zawartości do wielu profilów kodowania (H.264 linii bazowej, H.264 wysoki, AACL, AACH, Dolby Digital Plus) i wielokrotnych jakości. Jednak nie wszystkie urządzenia klienta będzie obsługiwać profilów wszystkich elementów zawartości i szybkości transmisji. Starsze urządzenia z systemem Android obsługują tylko H.264 linii bazowej + AACL. Wysyłanie wyższe szybkości transmisji na urządzeniu, którego nie można uzyskać korzyści, marnuje obliczenia przepustowości i urządzeń. Takie urządzenia muszą dekodowania wszystkie dane informacje tylko do skalowania w dół do wyświetlenia.

Manifestów dynamicznych, umożliwia tworzenie profilów urządzeń takich jak aplikacje mobilne, konsoli HD/SD itp. i obejmuje ścieżek i klas, które mają być częścią każdego profilu.

![Odwzorowanie filtrowanie przykład][renditions2]

W poniższym przykładzie koder użytego do kodowania zasobów mezzanine do siedmiu odwzorowaniami wideo każdego pliku MP4 z ISO (z 180p, aby 1080p). Zakodowanym elementem zawartości można dynamicznie spakowane do żadnego z następujących protokołów: HLS, Smooth i MPEG DASH.  W górnej części diagramu są wyświetlane manifest HLS dla zasobu bez filtrów (zawiera wszystkie wersje siedem).  W lewym dolnym rogu manifest HLS, do którego zastosowano filtr o nazwie "ott" jest wyświetlana. Filtr "ott" Określa, aby usunąć wszystkie różnych poniżej 1 MB/s, co spowodowało dolnej dwa poziomy jakości jest usunięta, a w odpowiedzi. W prawym dolnym rogu manifest HLS, do którego zastosowano filtr o nazwie "mobilnymi" jest wyświetlany. Filtr "przenośnych" Określa usunięcie wersji, gdy rozwiązanie jest większy niż 720p, co spowodowało dwóch wersji 1080p jest odłączony.

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

## <a name="create-filters-programmatically"></a>Programowe tworzenie filtrów
Omówiono następujące jednostki usługi Media Services, które są powiązane z filtrów. Artykuł zawiera również jak programowo utworzyć filtry.  

[Tworzenie filtrów za pomocą interfejsów API REST](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Łączenie wielu filtrów (skład filtru)
Można także połączyć wiele filtrów w pojedynczego adresu URL. 

Następujący scenariusz pokazuje, dlaczego warto połączyć filtrów:

1. Należy filtrować swoje jakość wideo dla urządzeń przenośnych, takich jak Android i urządzenie iPAD (w celu ograniczenia jakość wideo). Aby usunąć jakościami niepożądane, należy utworzyć filtr globalny odpowiednie dla profilów urządzeń. Jak wspomniano wcześniej w tym artykule, filtry globalne może służyć do wszystkich zasobów w ramach tego samego konta usługi media services bez dalszej skojarzenia. 
2. Chcesz także trim godzina rozpoczęcia i zakończenia elementu zawartości. Aby to osiągnąć, czy utworzyć filtr lokalnych i ustawić czas rozpoczęcia/zakończenia. 
3. Aby połączyć oba te filtry (bez kombinacji, należy dodać jakości filtru, aby filtr przycinania, co utrudnia użycie filtru).

Aby połączyć filtry, musisz ustawić nazw filtrów do manifestu/odtwarzania adres URL z rozdzielaną średnikami. Załóżmy, że masz filtr o nazwie *MyMobileDevice* która filtruje jakość i inny o nazwie *MyStartTime* można ustawić określony czas rozpoczęcia. Można połączyć je w następujący sposób:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Można połączyć maksymalnie trzy filtry. 

Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogu.

## <a name="know-issues-and-limitations"></a>Wiedzieć, problemy i ograniczenia
* Dynamiczne manifest działa w GOP granic (klucz ramki), dlatego przycinania ma GOP dokładności. 
* Można użyć tej samej nazwy filtru lokalne i globalne filtry. Lokalne filtry mają wyższy priorytet i spowoduje przesłonięcie filtrów globalnych.
* Jeśli zaktualizujesz filtru, może potrwać do 2 minut, zanim punkt końcowy przesyłania strumieniowego odświeżyć zasady. Zawartość zostało obsłużone za pomocą niektóre filtry (i w pamięci podręcznej serwerów proxy i Azure CDN pamięci podręcznych), aktualizowanie te filtry może spowodować błędy odtwarzacza. Zalecane jest, aby wyczyścić pamięć podręczną po zaktualizowaniu filtru. Jeśli ta opcja nie jest możliwe, należy wziąć pod uwagę przy użyciu innego filtru.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Dostarczanie zawartości z klientów — Przegląd](media-services-deliver-content-overview.md)

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
