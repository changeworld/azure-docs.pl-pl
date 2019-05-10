---
title: Filtry i usługi Azure Media Services o nazwie manifesty dynamiczne | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób tworzenia filtrów, więc klienta można ich używać do określonych sekcji strumienia strumienia. Usługa Media Services tworzy manifestów dynamicznych w celu uzyskania tego selektywne przesyłania strumieniowego.
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 21fb2b84fd58fb7cca7551ee1cef0c79179cfa40
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467130"
---
# <a name="dynamic-manifests"></a>Manifesty dynamiczne

Usługa Media Services oferuje **manifestów dynamicznych** oparte na wstępnie zdefiniowane filtry. Po zdefiniowaniu filtry (zobacz [zdefiniować filtry](filters-concept.md)), klienci można korzystać z ich do przesyłania strumieniowego określonej wersji lub podrzędnych klipów wideo. Filtry mogą określić w adresie URL przesyłania strumieniowego. Filtry można zastosować do adaptacyjną szybkością transmisji bitów, protokołów przesyłania strumieniowego: Apple HTTP Live przesyłania strumieniowego (HLS), między innymi MPEG-DASH i Smooth Streaming. 

W poniższej tabeli przedstawiono przykładowe adresy URL przy użyciu filtrów:

|Protokół|Przykład|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

> [!NOTE]
> Nie zmieniaj manifestów dynamicznych, elementu zawartości i domyślny manifest dla tego zasobu. Klienta można zażądać strumienia z lub bez filtrów. 
> 

W tym temacie opisano pojęcia związane z **manifestów dynamicznych** i zawiera przykładowe scenariusze, w których warto korzystać z tej funkcji.

## <a name="manifests-overview"></a>Omówienie manifestów

Usługa Media Services obsługuje HLS, MPEG DASH, Smooth Streaming protokołów. Jako część [funkcję dynamicznego tworzenia pakietów](dynamic-packaging-overview.md), przesyłania strumieniowego manifesty na kliencie (listy odtwarzania wzorca HLS, DASH Media prezentacji opis (MPD) i Smooth Streaming) generowanych dynamicznie w oparciu o wybór formatu w adresie URL. Zobacz protokoły dostarczania w [w tej sekcji](dynamic-packaging-overview.md#delivery-protocols). 

### <a name="get-and-examine-manifest-files"></a>Pobierz i sprawdź plik manifestu

Należy określić listę warunków właściwość śledzenie filtru podstawie, na którym ścieżki strumienia (na żywo lub wideo na żądanie) powinny zostać uwzględnione w manifeście utworzony dynamicznie. Aby uzyskać i zbadać właściwości ścieżki, musisz najpierw załadować manifestu Smooth Streaming.

[Przekazywanie, kodowanie i przesyłanie strumieniowe plików przy użyciu platformy .NET](stream-files-tutorial-with-api.md) samouczka dowiesz się, jak tworzyć adresy URL przesyłania strumieniowego przy użyciu platformy .NET (znajduje się pozycja [tworzenie adresów URL](stream-files-tutorial-with-api.md#get-streaming-urls) sekcji). Po uruchomieniu aplikacji, wskazuje jeden z adresów URL w manifeście Smooth Streaming: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/>Skopiuj i wklej adres URL w pasku adresu przeglądarki. Plik zostanie pobrany. Możesz otworzyć go w wybranym edytorze tekstów.

Na przykład REST, zobacz [przekazywanie, kodowanie i przesyłanie strumieniowe plików z użyciem usług REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitor szybkości transmisji bitów strumienia wideo

Możesz użyć [strony pokaz usługi Azure Media Player](https://aka.ms/amp) monitorowanie szybkości transmisji bitów strumienia wideo. Na stronie wersji demonstracyjnej są wyświetlane informacje diagnostyczne w **diagnostyki** karty:

![Diagnostyka Azure Media Player][amp_diagnostics]

## <a name="rendition-filtering"></a>Filtrowania wyświetlania

Istnieje możliwość kodowanie elementów zawartości do wielu profilów kodowania (H.264 linii bazowej, H.264 wysoki, AACL, AACH, Dolby Digital Plus) i wielokrotnych jakości. Jednak nie wszystkie urządzenia klienta będzie obsługiwać profilów wszystkich elementów zawartości i szybkości transmisji. Starsze urządzenia z systemem Android obsługują tylko H.264 linii bazowej + AACL. Wysyłanie wyższe szybkości transmisji do urządzenia, które nie może uzyskać korzyści, marnuje przepustowości i obliczanie urządzenia. Takie urządzenia muszą dekodowania wszystkie dane informacje tylko do skalowania w dół do wyświetlenia.

Manifestów dynamicznych, umożliwia tworzenie profilów urządzeń takich jak aplikacje mobilne, konsoli HD/SD itp. i obejmuje ścieżek i klas, które mają być częścią każdego profilu.

![Odwzorowanie filtrowanie przykład][renditions2]

W poniższym przykładzie koder użytego do kodowania zasobów mezzanine do siedmiu odwzorowaniami wideo każdego pliku MP4 z ISO (z 180p, aby 1080p). Zakodowanym elementem zawartości może być [dynamicznie spakowanych](dynamic-packaging-overview.md) do żadnego z następujących protokołów: HLS, MPEG DASH i Smooth.  W górnej części diagramu są wyświetlane manifest HLS dla zasobu bez filtrów (zawiera wszystkie wersje siedem).  W lewym dolnym rogu manifest HLS, do którego zastosowano filtr o nazwie "ott" jest wyświetlana. Filtr "ott" Określa, aby usunąć wszystkie różnych poniżej 1 MB/s, co spowodowało dolnej dwa poziomy jakości jest usunięta, a w odpowiedzi. W prawym dolnym rogu manifest HLS, do którego zastosowano filtr o nazwie "mobilnymi" jest wyświetlany. Filtr "przenośnych" Określa usunięcie wersji, gdy rozwiązanie jest większy niż 720p, co spowodowało dwóch wersji 1080p jest odłączony.

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

## <a name="associate-filters-with-streaming-locator"></a>Kojarzenie filtrów z lokalizatora przesyłania strumieniowego

Można określić listę filtrów zasobów lub konta, które będzie dotyczyć Twojego lokalizatora przesyłania strumieniowego. [Funkcji dynamicznego pakowania](dynamic-packaging-overview.md) ma zastosowanie ta lista filtrów wraz z tymi klienta określa się w adresie URL. Ta kombinacja generuje [dynamiczne manifest](filters-dynamic-manifest-overview.md), która jest oparta na filtry w adresie URL i filtry, możesz określić na lokalizatora przesyłania strumieniowego. Zaleca się korzystania z tej funkcji, jeśli chcesz zastosować filtry, ale nie należy udostępniać nazwy filtru w adresie URL.

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
