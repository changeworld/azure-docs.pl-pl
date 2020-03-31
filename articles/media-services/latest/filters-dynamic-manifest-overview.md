---
title: Filtrowanie manifestów przy użyciu pakietu dynamic
titleSuffix: Azure Media Services
description: Dowiedz się, jak tworzyć filtry przy użyciu pakietu dynamic do filtrowania i selektywnego przesyłania strumieniowego manifestów.
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
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: cd955f97a2f26543f799d95b7dc0b1de235333c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186217"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>Filtrowanie manifestów przy użyciu pakietu dynamic

Podczas dostarczania adaptacyjnej zawartości strumieniowej szybkości transmisji bitów do urządzeń czasami trzeba opublikować wiele wersji manifestu, aby kierować określone możliwości urządzenia lub dostępną przepustowość sieci. [Pakiet dynamic](dynamic-packaging-overview.md) umożliwia określenie filtrów, które mogą odfiltrować określone kodeki, rozdzielczości, szybkości transmisji bitów i kombinacje ścieżek audio w locie. To filtrowanie eliminuje konieczność tworzenia wielu kopii. Wystarczy opublikować nowy adres URL z określonym zestawem filtrów skonfigurowanych do urządzeń docelowych (iOS, Android, SmartTV lub przeglądarki) i możliwości sieci (scenariusze o wysokiej przepustowości, telefonii komórkowej lub niskiej przepustowości). W takim przypadku klienci mogą manipulować przesyłania strumieniowego zawartości za pośrednictwem ciągu zapytania (określając dostępne [filtry zasobów lub filtry konta)](filters-concept.md)i używać filtrów do przesyłania strumieniowego określonych sekcji strumienia.

Niektóre scenariusze dostarczania wymagają upewnienia się, że klient nie może uzyskać dostępu do określonych ścieżek. Na przykład może nie chcesz publikować manifestu, który zawiera ścieżki HD do określonej warstwy subskrybenta. Możesz też usunąć określone adaptacyjne ścieżki szybkości transmisji bitów (ABR), aby zmniejszyć koszty dostawy do określonego urządzenia, które nie skorzysta z dodatkowych ścieżek. W takim przypadku można skojarzyć listę wstępnie utworzonych filtrów z [lokalizatorem przesyłania strumieniowego](streaming-locators-concept.md) podczas tworzenia. Klienci nie mogą manipulować sposób przesyłania strumieniowego zawartości, ponieważ jest ona zdefiniowana przez **lokalizator przesyłania strumieniowego.**

Filtrowanie można łączyć poprzez określanie [filtrów w lokalizatorze przesyłania strumieniowego](filters-concept.md#associating-filters-with-streaming-locator) + dodatkowe filtry specyficzne dla urządzenia określone przez klienta w adresie URL. Ta kombinacja jest przydatna do ograniczania dodatkowych ścieżek, takich jak metadane lub strumienie zdarzeń, języki audio lub opisowe ścieżki audio.

Ta możliwość określania różnych filtrów w strumieniu zapewnia zaawansowane rozwiązanie do manipulowania **manifestem dynamicznym,** które będzie dotyczyć wielu scenariuszy przypadków użycia dla urządzeń docelowych. W tym temacie opisano pojęcia związane z **manifestami dynamicznymi** i podano przykłady scenariuszy, w których można użyć tej funkcji.

> [!NOTE]
> Manifesty dynamiczne nie zmieniają zasobu i domyślnego manifestu dla tego zasobu.

## <a name="overview-of-manifests"></a>Przegląd manifestów

Usługa Azure Media Services obsługuje protokoły HLS, MPEG DASH i Smooth Streaming. W ramach [dynamicznego pakowania](dynamic-packaging-overview.md)manifesty klienta przesyłania strumieniowego (główna lista odtwarzania HLS, opis prezentacji multimediów DASH [MPD] i płynne przesyłanie strumieniowe) są generowane dynamicznie na podstawie selektora formatu w adresie URL. Aby uzyskać więcej informacji, zobacz protokoły dostarczania w [typowym przepływie pracy na żądanie](dynamic-packaging-overview.md#delivery-protocols).

### <a name="get-and-examine-manifest-files"></a>Pobierz i sprawdź pliki manifestów

Należy określić listę warunków właściwości śledzenia śledzenia filtru na podstawie tego, które ścieżki strumienia (na żywo lub wideo na żądanie [VOD]) powinny być uwzględnione w dynamicznie utworzony manifest. Aby uzyskać i zbadać właściwości ścieżek, należy najpierw załadować smooth streaming manifestu.

[Przycisk Przekaż, zakoduj i strumieniuj pliki za pomocą](stream-files-tutorial-with-api.md#get-streaming-urls) samouczka .NET pokazuje, jak tworzyć adresy URL przesyłania strumieniowego za pomocą platformy .NET. Po uruchomieniu aplikacji jeden z adresów URL wskazuje manifest `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`Płynne przesyłanie strumieniowe: .<br/> Skopiuj i wklej adres URL na pasku adresu przeglądarki. Plik zostanie pobrany. Można go otworzyć w dowolnym edytorze tekstu.

W przykładzie REST zobacz [Przekazywanie, kodowanie i przesyłanie strumieniowe plików za pomocą REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitorowanie szybkości transmisji bitów strumienia wideo

Za pomocą [strony demonstracyjnej programu Azure Media Player](https://aka.ms/azuremediaplayer) można monitorować szybkość transmisji bitów strumienia wideo. Na stronie demonstracyjnej są wyświetlane informacje diagnostyczne na karcie **Diagnostyka:**

![Diagnostyka programu Azure Media Player][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>Przykłady: adresy URL z filtrami w ciągu zapytania

Filtry można zastosować do protokołów przesyłania strumieniowego ABR: HLS, MPEG-DASH i Smooth Streaming. W poniższej tabeli przedstawiono kilka przykładów adresów URL z filtrami:

|Protocol (Protokół)|Przykład|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Filtrowanie wersji

Zasób można zakodować do wielu profili kodowania (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) i wielu szybkości transmisji bitów wysokiej jakości. Jednak nie wszystkie urządzenia klienckie będą obsługiwać wszystkie profile i szybkość transmisji bitów zasobu. Na przykład starsze urządzenia z systemem Android obsługują tylko H.264 Baseline +AACL. Wysyłanie wyższych szybkości transmisji bitów do urządzenia, które nie może uzyskać korzyści, marnuje przepustowość i obliczenia urządzenia. Takie urządzenie musi dekodować wszystkie podane informacje, tylko po to, aby je skalować w dół w celu wyświetlenia.

W programie Dynamic Manifest można tworzyć profile urządzeń (takie jak urządzenia mobilne, konsole lub HD/SD) oraz dołączać ścieżki i cechy, które mają być częścią każdego profilu. To się nazywa filtrowanie wersji. Na poniższym diagramie przedstawiono przykład.

![Przykład filtrowania wersji za pomocą manifestu dynamicznego][renditions2]

W poniższym przykładzie koder został użyty do zakodowania zasobu antresoli w siedmiu wersjach wideo ISO MP4s (od 180p do 1080p). Zakodowany zasób może być [dynamicznie pakowany](dynamic-packaging-overview.md) do dowolnego z następujących protokołów przesyłania strumieniowego: HLS, MPEG DASH i Smooth.

W górnej części poniższego diagramu przedstawiono manifest HLS dla zasobu bez filtrów. (Zawiera wszystkie siedem wersji.)  W lewym dolnym lewym diagramie pokazano manifest HLS, do którego zastosowano filtr o nazwie "ott". Filtr "ott" określa usunięcie wszystkich szybkości transmisji bitów poniżej 1 Mb/s, więc dwa dolne poziomy jakości zostały usunięte w odpowiedzi. W prawym dolnym prawym diagramie pokazano manifest HLS, do którego zastosowano filtr o nazwie "mobile". Filtr "mobilny" określa usunięcie wersji, w których rozdzielczość jest większa niż 720p, więc dwie wersje 1080p zostały usunięte.

![Filtrowanie odwzorowania za pomocą manifestu dynamicznego][renditions1]

## <a name="removing-language-tracks"></a>Usuwanie ścieżek językowych
Zasoby mogą zawierać wiele języków audio, takich jak angielski, hiszpański, francuski itd. Zazwyczaj sdk odtwarzacza zarządza domyślnym wyborem ścieżki dźwiękowej i dostępnymi ścieżkami audio na wybór użytkownika.

Tworzenie takich sdk odtwarzacza jest trudne, ponieważ wymaga różnych implementacji w ramach odtwarzacza specyficzne dla urządzenia. Ponadto na niektórych platformach interfejsy API odtwarzacza są ograniczone i nie zawierają funkcji wyboru dźwięku, w której użytkownicy nie mogą wybrać ani zmienić domyślnej ścieżki dźwiękowej. Za pomocą filtrów zasobów można kontrolować zachowanie, tworząc filtry, które zawierają tylko żądane języki audio.

![Filtrowanie ścieżek językowych za pomocą manifestu dynamicznego][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Przycinanie początku zasobu

W większości wydarzeń transmisji strumieniowej na żywo operatorzy uruchomić niektóre testy przed rzeczywistym zdarzenia. Na przykład mogą one zawierać łupków tak przed rozpoczęciem zdarzenia: "Program rozpocznie się na chwilę."

Jeśli program jest archiwizacji, dane testowe i łupków są również archiwizowane i zawarte w prezentacji. Jednak te informacje nie powinny być wyświetlane klientom. Za pomocą manifestu dynamicznego można utworzyć filtr czasu rozpoczęcia i usunąć niechciane dane z manifestu.

![Rozpoczęcie przycinania zasobu za pomocą manifestu dynamicznego][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Tworzenie podkompani (widoków) z archiwum na żywo

Wiele wydarzeń na żywo jest długotrwałych, a archiwum na żywo może zawierać wiele zdarzeń. Po zakończeniu wydarzenia na żywo nadawcy mogą chcieć podzielić archiwum na żywo na logiczne sekwencje uruchamiania i zatrzymywania programu.

Można publikować te programy wirtualne oddzielnie bez przetwarzania końcowego archiwum na żywo i nie tworzenia oddzielnych zasobów (które nie przynoszą korzyści z istniejących fragmentów pamięci podręcznej w sieciach CDN). Przykładami takich wirtualnych programów są kwatery gry w piłkę nożną lub koszykówkę, inningi w baseballu lub pojedyncze wydarzenia dowolnego programu sportowego.

Za pomocą manifestu dynamicznego można tworzyć filtry przy użyciu czasów rozpoczęcia/zakończenia i tworzyć widoki wirtualne na górze archiwum na żywo.

![Filtr podpoślizgowy z manifestem dynamicznym][subclip_filter]

Oto filtrowany zasób:

![Filtrowany zasób z manifestem dynamicznym][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Dostosowywanie okna prezentacji (DVR)

Obecnie usługa Azure Media Services oferuje archiwum okrężne, w którym czas trwania można skonfigurować w zakresie od 1 minuty do 25 godzin. Filtrowanie manifestów może służyć do tworzenia toczącego się okna DVR w górnej części archiwum, bez usuwania nośnika. Istnieje wiele scenariuszy, w których nadawcy chcą zapewnić ograniczone okno DVR, aby poruszać się z krawędzią na żywo, a jednocześnie zachować większe okno archiwizacji. Nadawca może chcieć użyć danych, które są poza oknem DVR, aby wyróżnić klipy, lub może chcieć udostępnić różne okna DVR dla różnych urządzeń. Na przykład większość urządzeń przenośnych nie obsługuje dużych okien DVR (można mieć 2-minutowe okno DVR dla urządzeń przenośnych i jedną godzinę dla klientów stacjonarnych).

![Okno DVR z manifestem dynamicznym][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Regulacja LiveBackoff (pozycja na żywo)

Filtrowanie manifestów może służyć do usuwania kilku sekund z aktywnej krawędzi programu na żywo. Filtrowanie umożliwia nadawcom oglądanie prezentacji w punkcie publikacji podglądu i tworzenie punktów wstawiania reklam, zanim widzowie otrzymają strumień (cofnięty o 30 sekund). Nadawcy mogą następnie wypchnąć te reklamy do swoich platform klientów w czasie, aby mogli otrzymywać i przetwarzać informacje przed możliwością reklamy.

Oprócz obsługi reklam, ustawienie back-off na żywo może służyć do regulacji pozycji widzów tak, aby klienci dryfować i trafić na żywo krawędzi, nadal mogą uzyskać fragmenty z serwera. W ten sposób klienci nie otrzymają błędu HTTP 404 lub 412.

![Filtr do wycofywania na żywo z manifestem dynamicznym][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Łączenie wielu reguł w jednym filtrze

W jednym filtrze można połączyć wiele reguł filtrowania. Na przykład można zdefiniować "regułę zakresu", aby usunąć plansze z archiwum na żywo, a także odfiltrować dostępne szybkości transmisji bitów. Gdy stosujesz wiele reguł filtrowania, wynikiem końcowym jest przecięcie wszystkich reguł.

![Wiele reguł filtrowania z manifestem dynamicznym][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Łączenie wielu filtrów (skład filtra)

Można również połączyć wiele filtrów w jednym adresie URL. Poniższy scenariusz pokazuje, dlaczego warto połączyć filtry:

1. Musisz filtrować swoje cechy wideo dla urządzeń mobilnych, takich jak Android lub iPad (w celu ograniczenia jakości wideo). Aby usunąć niepożądane cechy, utworzysz filtr konta odpowiedni dla profili urządzeń. Filtry kont można używać dla wszystkich zasobów w ramach tego samego konta usługi Media Services bez dalszego skojarzenia.
1. Chcesz również przyciąć godzinę rozpoczęcia i zakończenia zasobu. Aby wykonać przycinanie, utworzysz filtr zasobów i ustaw godzinę rozpoczęcia/zakończenia.
1. Chcesz połączyć oba te filtry. Bez kombinacji, trzeba by dodać jakość filtrowania do filtru przycinania, co utrudniłoby użycie filtra.

Aby połączyć filtry, ustaw nazwy filtrów na adres URL manifestu/listy odtwarzania w formacie rozdzielanym średnikami. Załóżmy, że masz filtr o nazwie *MyMobileDevice,* który filtruje jakości, a masz inny o nazwie *MyStartTime,* aby ustawić określony czas rozpoczęcia. Można połączyć maksymalnie trzy filtry.

Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Istotne kwestie i ograniczenia

- Wartości **forceEndTimestamp**, **presentationWindowDuration**i **liveBackoffDuration** nie powinny być ustawione dla filtru VOD. Są one używane tylko w scenariuszach filtrów na żywo.
- Manifest dynamiczny działa w granicach GOP (klatki kluczowe), więc przycinanie ma dokładność GOP.
- Tej samej nazwy filtru można użyć dla filtrów kont i zasobów. Filtry zasobów mają wyższy priorytet i zastępują filtry kont.
- Jeśli zaktualizujesz filtr, odświeżenie reguł może potrwać do 2 minut. Jeśli do obsługi zawartości użyto filtrów (a zawartość została buforowana w plikach proxy i pamięci podręcznych sieci CDN), zaktualizowanie tych filtrów może spowodować błędy odtwarzacza. Zaleca się wyczyszczenie pamięci podręcznej po zaktualizowaniu filtru. Jeśli ta opcja nie jest możliwa, należy rozważyć użycie innego filtru.
- Klienci muszą ręcznie pobrać manifest i przeanalizować dokładną sygnaturę czasu rozpoczęcia i skalę czasu.

    - Aby określić właściwości ścieżek w zasobie, [pobierz i sprawdź plik manifestu](#get-and-examine-manifest-files).
    - Formuła, aby ustawić właściwości sygnatury czasowej filtru zasobów jest: <br/>startTimestamp &lt;= czas rozpoczęcia&gt; +  &lt;w oczekiwanym&gt; czasie rozpoczęcia filtru manifestu w sekundach * skala czasu

## <a name="next-steps"></a>Następne kroki

W poniższych artykułach pokazano, jak programowo tworzyć filtry:  

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
