---
title: Filtry i manifesty dynamiczne | Dokumenty firmy Microsoft
description: W tym temacie opisano sposób tworzenia filtrów, aby klient mógł ich używać do przesyłania strumieniowego określonych sekcji strumienia. Usługa Media Services tworzy dynamiczne manifesty w celu zarchiwizowania tego selektywnego przesyłania strumieniowego.
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
ms.author: juliako
ms.openlocfilehash: 1234263fa800a17d0a5c235df54ca2751e3094bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69015852"
---
# <a name="filters-and-dynamic-manifests"></a>Filtry i manifesty dynamiczne

> [!div class="op_single_selector" title1="Wybierz używana wersja usługi Media Services:"]
> * [Wersja 2](media-services-dynamic-manifest-overview.md)
> * [Wersja 3](../latest/filters-dynamic-manifest-overview.md)

Począwszy od wersji 2.17, program Media Services umożliwia definiowanie filtrów dla zasobów. Filtry te są regułami po stronie serwera, które pozwolą klientom wybrać takie czynności, jak: odtworzenie tylko części wideo (zamiast odtwarzania całego filmu) lub określenie tylko podzbioru wersji audio i wideo, które może obsługiwać urządzenie klienta ( zamiast wszystkich wersji skojarzonych z zasobem). To filtrowanie zasobów odbywa się za pomocą **manifestów dynamicznych,** które są tworzone na żądanie klienta w celu przesyłania strumieniowego wideo na podstawie określonych filtrów.

W tym temacie omówiono typowe scenariusze, w których przy użyciu filtrów byłoby korzystne dla klientów i łącza do tematów, które pokazują, jak tworzyć filtry programowo.

## <a name="overview"></a>Omówienie
Podczas dostarczania treści klientom (przesyłania strumieniowego wydarzeń na żywo lub wideo na żądanie) twoim celem jest dostarczanie wysokiej jakości wideo na różnych urządzeniach w różnych warunkach sieciowych. Aby osiągnąć ten cel, wykonaj następujące czynności:

* zakodować strumień do wielosankowego[(adaptacyjnego bitrate)](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)strumienia wideo (zajmie to jakość i warunki sieciowe) oraz 
* użyj pakietu [Dynamic Packaging](media-services-dynamic-packaging-overview.md) usługi Media Services, aby dynamicznie przepakować strumień do różnych protokołów (zajmie to się przesyłaniem strumieniowe na różnych urządzeniach). Usługa Media Services obsługuje dostarczanie następujących technologii adaptacyjnego przesyłania strumieniowego szybkości transmisji bitów: HTTP Live Streaming (HLS), Smooth Streaming i MPEG DASH. 

### <a name="manifest-files"></a>Pliki manifestów
Podczas kodowania zasobu w celu adaptacyjnego przesyłania strumieniowego szybkości transmisji bitów tworzony jest plik **manifestu** (listy odtwarzania) (plik jest oparty na tekście lub XML). Plik **manifestu** zawiera metadane przesyłania strumieniowego, takie jak: typ ścieżki (audio, wideo lub tekst), nazwa ścieżki, czas rozpoczęcia i zakończenia, szybkość transmisji bitów (cechy), języki ścieżek, okno prezentacji (okno przesuwne o stałym czasie trwania), kodek wideo (FourCC). Nakazuje również graczowi pobranie następnego fragmentu, dostarczając informacji o następnych dostępnych fragmentach wideo i ich lokalizacji. Fragmenty (lub segmenty) są rzeczywistymi "fragmentami" zawartości wideo.

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
Istnieją [scenariusze,](media-services-dynamic-manifest-overview.md#scenarios) gdy klient potrzebuje większej elastyczności niż opisane w pliku manifestu domyślnego zasobu. Przykład:

* Specyficzne dla urządzenia: dostarczaj tylko określone wersje i/lub określone ścieżki językowe obsługiwane przez urządzenie używane do odtwarzania zawartości ("filtrowanie wersji"). 
* Zmniejsz manifest, aby wyświetlić podkomiks zdarzenia na żywo ("filtrowanie podklipów").
* Przycinanie początku filmu ("przycinanie wideo").
* Dostosuj okno prezentacji (DVR) w celu zapewnienia ograniczonej długości okna rejestratora w odtwarzaczu ("dostosowujące okno prezentacji").

Aby osiągnąć tę elastyczność, usługa Media Services oferuje **dynamiczne manifesty** oparte na wstępnie [zdefiniowanych filtrach.](media-services-dynamic-manifest-overview.md#filters)  Po zdefiniowaniu filtrów klienci mogą używać ich do przesyłania strumieniowego określonego odwzorowania lub podkomplizowania wideo. Określałyby one filtry w adresie URL przesyłania strumieniowego. Filtry mogą być stosowane do adaptacyjnych protokołów przesyłania strumieniowego szybkości transmisji bitów obsługiwanych przez [dynamic packaging:](media-services-dynamic-packaging-overview.md)HLS, MPEG-DASH i Smooth Streaming. Przykład:

Adres URL MPEG DASH z filtrem

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

Płynny adres URL przesyłania strumieniowego z filtrem

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Aby uzyskać więcej informacji na temat dostarczania zawartości i tworzenia adresów URL przesyłania strumieniowego, zobacz [Omówienie dostarczania zawartości](media-services-deliver-content-overview.md).

> [!NOTE]
> Należy zauważyć, że manifesty dynamiczne nie zmieniają zasobu i domyślnego manifestu dla tego zasobu. Klient może zażądać strumienia z filtrami lub bez. 
> 
> 

### <a name="filters"></a><a id="filters"></a>Filtry
Istnieją dwa typy filtrów zasobów: 

* Filtry globalne (mogą być stosowane do dowolnego zasobu na koncie usługi Azure Media Services, mają okres istnienia konta) i 
* Filtry lokalne (mogą być stosowane tylko do zasobu, z którym filtr był skojarzony podczas tworzenia, mają okres istnienia zasobu). 

Globalne i lokalne typy filtrów mają dokładnie takie same właściwości. Główną różnicą między tymi dwoma jest to, dla których scenariusze, jaki typ filera jest bardziej odpowiedni. Filtry globalne są zazwyczaj odpowiednie dla profili urządzeń (filtrowanie wersji), w których można użyć filtrów lokalnych do przycinania określonego zasobu.

## <a name="common-scenarios"></a><a id="scenarios"></a>Typowe scenariusze
Jak wspomniano wcześniej, podczas dostarczania treści klientom (przesyłanie strumieniowe wydarzeń na żywo lub wideo na żądanie) Twoim celem jest dostarczanie wysokiej jakości wideo do różnych urządzeń w różnych warunkach sieciowych. Ponadto mogą mieć inne wymagania, które obejmują filtrowanie zasobów i używanie **manifestu dynamicznego**s. W poniższych sekcjach przedstawiono krótki przegląd różnych scenariuszy filtrowania.

* Określ tylko podzbiór wersji audio i wideo, które mogą obsługiwać niektóre urządzenia (zamiast wszystkich wersji skojarzonych z zasobem). 
* Odtwarzanie tylko części filmu (zamiast odtwarzania całego filmu).
* Dostosuj okno prezentacji DVR.

## <a name="rendition-filtering"></a>Filtrowanie wersji
Zasób można zakodować do wielu profili kodowania (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) i wielu szybkości transmisji bitów wysokiej jakości. Jednak nie wszystkie urządzenia klienckie będą obsługiwać wszystkie profile i szybkość transmisji bitów zasobu. Na przykład starsze urządzenia z systemem Android obsługują tylko H.264 Baseline+AACL. Wysyłanie wyższych szybkości transmisji bitów do urządzenia, które nie może uzyskać korzyści, marnuje przepustowość i obliczenia urządzenia. Takie urządzenie musi dekodować wszystkie podane informacje, tylko po to, aby je skalować w dół w celu wyświetlenia.

Za pomocą manifestu dynamicznego można tworzyć profile urządzeń, takie jak urządzenia mobilne, konsole, HD/SD itp., a także uwzględniać ścieżki i cechy, które mają być częścią każdego profilu.

![Przykład filtrowania wersji][renditions2]

W poniższym przykładzie koder został użyty do zakodowania zasobu antresoli w siedmiu wersjach wideo ISO MP4s (od 180p do 1080p). Zakodowany zasób może być dynamicznie pakowany do dowolnego z następujących protokołów przesyłania strumieniowego: HLS, Smooth i MPEG DASH.  W górnej części diagramu wyświetlany jest manifest HLS dla zasobu bez filtrów (zawiera wszystkie siedem wersji).  W lewym dolnym rogu wyświetlany jest manifest HLS, do którego zastosowano filtr o nazwie "ott". Filtr "ott" określa usunięcie wszystkich szybkości transmisji bitów poniżej 1Mbps, co spowodowało, że dwa dolne poziomy jakości zostały usunięte w odpowiedzi. W prawym dolnym rogu wyświetlany jest manifest HLS, do którego zastosowano filtr o nazwie "mobile". Filtr "mobilny" określa usunięcie wersji, w których rozdzielczość jest większa niż 720p, co spowodowało usunięcie dwóch wersji 1080p.

![Filtrowanie wersji][renditions1]

## <a name="removing-language-tracks"></a>Usuwanie ścieżek językowych
Zasoby mogą zawierać wiele języków audio, takich jak angielski, hiszpański, francuski itp. Zazwyczaj menedżerów SDK odtwarzacza domyślny wybór ścieżki audio i dostępne ścieżki audio na wybór użytkownika. Jest to trudne do opracowania takich SDK odtwarzacza, wymaga różnych implementacji w ramach odtwarzacza specyficzne dla urządzenia. Ponadto na niektórych platformach interfejsy API odtwarzacza są ograniczone i nie zawierają funkcji wyboru dźwięku, w której użytkownicy nie mogą wybrać ani zmienić domyślnej ścieżki dźwiękowej. Za pomocą filtrów zasobów można kontrolować zachowanie, tworząc filtry, które zawierają tylko żądane języki audio.

![Śledzenie języka filtrowania][language_filter]

## <a name="trimming-start-of-an-asset"></a>Rozpoczęcie przycinania zasobu
W większości wydarzeń transmisji strumieniowej na żywo operatorzy uruchomić niektóre testy przed rzeczywistym zdarzenia. Na przykład mogą one zawierać łupków tak przed rozpoczęciem zdarzenia: "Program rozpocznie się na chwilę". Jeśli program jest archiwizacji, dane testowe i łupków są również archiwizowane i zawarte w prezentacji. Jednak te informacje nie powinny być wyświetlane klientom. Za pomocą manifestu dynamicznego można utworzyć filtr czasu rozpoczęcia i usunąć niechciane dane z manifestu.

![Rozpoczęcie przycinania][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Tworzenie podkompani (widoków) z archiwum na żywo
Wiele wydarzeń na żywo jest długotrwałych, a archiwum na żywo może zawierać wiele zdarzeń. Po zakończeniu wydarzenia na żywo nadawcy mogą chcieć podzielić archiwum na żywo na logiczne sekwencje uruchamiania i zatrzymywania programu. Następnie należy opublikować te programy wirtualne oddzielnie bez przetwarzania końcowego archiwum na żywo i nie tworzenia oddzielnych zasobów (które nie korzystają z istniejących fragmentów pamięci podręcznej w sieciach CDN). Przykładami takich wirtualnych programów są kwatery gry w piłkę nożną lub koszykówkę, inningi w baseballu lub pojedyncze wydarzenia dowolnego programu sportowego.

Za pomocą manifestu dynamicznego można tworzyć filtry przy użyciu czasów rozpoczęcia/zakończenia i tworzyć widoki wirtualne na górze archiwum na żywo. 

![Filtr subclip][subclip_filter]

Filtrowany zasób:

![Narciarstwo][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Dostosowywanie okna prezentacji (DVR)
Obecnie usługa Azure Media Services oferuje archiwum okrężne, w którym czas trwania można skonfigurować w zakresie od 5 minut do 25 godzin. Filtrowanie manifestów może służyć do tworzenia toczącego się okna DVR w górnej części archiwum, bez usuwania nośnika. Istnieje wiele scenariuszy, w których nadawcy chcą zapewnić ograniczone okno DVR, aby poruszać się z krawędzią na żywo, a jednocześnie zachować większe okno archiwizacji. Nadawca może chcieć użyć danych, które są poza oknem DVR, aby wyróżnić klipy, lub może chcieć udostępnić różne okna DVR dla różnych urządzeń. Na przykład większość urządzeń przenośnych nie obsługuje dużych okien DVR (można mieć 2-minutowe okno DVR dla urządzeń przenośnych i jedną godzinę dla klientów stacjonarnych).

![Okno DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Regulacja LiveBackoff (pozycja na żywo)
Filtrowanie manifestów może służyć do usuwania kilku sekund z aktywnej krawędzi programu na żywo. Filtrowanie umożliwia nadawcom oglądanie prezentacji w punkcie publikacji podglądu i tworzenie punktów wstawiania reklam, zanim widzowie otrzymają strumień (wycofane o 30 sekund). Nadawcy mogą następnie wypchnąć te reklamy do swoich platform klientów w czasie, aby mogli otrzymać i przetworzyć informacje przed możliwością reklamy.

Oprócz obsługi reklam, LiveBackoff ustawienie może służyć do dostosowywania pozycji widzów tak, aby gdy klienci dryfować i trafić na żywo krawędzi mogą nadal uzyskać fragmenty z serwera zamiast uzyskiwania błędu HTTP 404 lub 412.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Łączenie wielu reguł w jednym filtrze
W jednym filtrze można połączyć wiele reguł filtrowania. Na przykład można zdefiniować "regułę zakresu", aby usunąć plansze z archiwum na żywo, a także odfiltrować dostępne szybkości transmisji bitów. Podczas stosowania wielu reguł filtrowania, wynikiem końcowym jest przecięcie wszystkich reguł.

![wiele reguł][multiple-rules]

## <a name="create-filters-programmatically"></a>Programowo twórz filtry
W poniższym artykule omówiono jednostki usługi Media Services, które są związane z filtrami. W artykule pokazano również, jak programowo tworzyć filtry.  

[Tworzenie filtrów za pomocą interfejsów API REST](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Łączenie wielu filtrów (skład filtra)
Można również połączyć wiele filtrów w jednym adresie URL. 

Poniższy scenariusz pokazuje, dlaczego warto połączyć filtry:

1. Musisz filtrować swoje cechy wideo dla urządzeń mobilnych, takich jak Android lub iPAD (w celu ograniczenia jakości wideo). Aby usunąć niepożądane cechy, należy utworzyć filtr globalny odpowiedni dla profili urządzeń. Jak wspomniano wcześniej w tym artykule, filtry globalne mogą być używane dla wszystkich zasobów w ramach tego samego konta usług multimedialnych bez dalszego skojarzenia. 
2. Chcesz również przyciąć godzinę rozpoczęcia i zakończenia zasobu. Aby to osiągnąć, należy utworzyć filtr lokalny i ustawić czas rozpoczęcia/zakończenia. 
3. Chcesz połączyć oba te filtry (bez kombinacji należy dodać filtrowanie jakości do filtru przycinania, co utrudnia użycie filtru).

Aby połączyć filtry, należy ustawić nazwy filtrów na adres URL manifestu/listy odtwarzania ze średnikami rozdzielanych. Załóżmy, że masz filtr o nazwie *MyMobileDevice,* który filtruje cechy i masz inny o nazwie *MyStartTime,* aby ustawić określony czas rozpoczęcia. Możesz połączyć je w ten sposób:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Można połączyć maksymalnie trzy filtry. 

Aby uzyskać więcej informacji, zobacz [ten](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a name="know-issues-and-limitations"></a>Poznaj problemy i ograniczenia
* Manifest dynamiczny działa w granicach GOP (klatki kluczowe), dlatego przycinanie ma dokładność GOP. 
* Tej samej nazwy filtru można używać dla filtrów lokalnych i globalnych. Filtry lokalne mają wyższy priorytet i zastępują filtry globalne.
* Jeśli zaktualizujesz filtr, odświeżenie reguł może potrwać do 2 minut. Jeśli zawartość została wyświetlona przy użyciu niektórych filtrów (i buforowane w proxy i pamięci podręcznej CDN), aktualizowanie tych filtrów może spowodować błędy odtwarzacza. Zaleca się, aby wyczyścić pamięć podręczną po zaktualizowaniu filtru. Jeśli ta opcja nie jest możliwa, należy rozważyć użycie innego filtru.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Dostarczanie treści do klientów — omówienie](media-services-deliver-content-overview.md)

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
