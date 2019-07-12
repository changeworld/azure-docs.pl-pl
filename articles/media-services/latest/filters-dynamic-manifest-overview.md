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
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: ee0200f7c007b437a27b8e9d0f36becc13b8f611
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835815"
---
# <a name="pre-filtering-manifests-by-using-dynamic-packager"></a>Wstępnie filtrowanie manifesty za pomocą funkcji dynamicznego pakowania

Gdy można dostarczać z adaptacyjną szybkością transmisji bitów, przesyłanie strumieniowe zawartości do urządzeń, często konieczne publikowanie wielu wersji manifestu możliwości określonego urządzenia docelowego lub dostępnej przepustowości sieci. [Funkcji dynamicznego pakowania](dynamic-packaging-overview.md) pozwala określić filtry, które można odfiltrować określonych koderów-dekoderów, rozdzielczości, szybkości transmisji i dźwięk śledzić kombinacje na bieżąco usunięcie konieczności tworzenia wielu kopii. Po prostu, należy opublikować nowy adres URL przy użyciu określonych filtrów pod kątem urządzenia docelowego (z systemem iOS, Android, SmartTV lub przeglądarki) i możliwości sieciowych (scenariusze o dużej przepustowości, mobilnych lub o niskiej przepustowości). W tym przypadku klienci można manipulować, przesyłanie strumieniowe zawartości za pomocą ciągu zapytania (, określając dostępny [filtry zasobów lub filtry konta](filters-concept.md)) i korzystaj z filtrów do określonych sekcji strumienia strumienia.

Niektóre scenariusze dostarczania wymagają, należy upewnić się, że klient nie może uzyskać dostępu do określonej ścieżki. Na przykład może nie chcesz opublikować manifestu, który zawiera HD ścieżki do warstwy określonych subskrybentów. Możesz też usunąć ścieżek określonych adaptacyjną szybkością transmisji bitów (ABR), aby ograniczyć koszty dostawy do określonego urządzenia, które nie będą korzystać z dodatkowych ścieżek. W takim przypadku można było skojarzyć listę filtrów wstępnie utworzonych za pomocą usługi [lokalizatora przesyłania strumieniowego](streaming-locators-concept.md) przy tworzeniu. W tym przypadku klienci nie można manipulować, jak są przesyłane strumieniowo zawartość, jest on definiowany przez **lokalizatora przesyłania strumieniowego**.

Można połączyć filtrowania poprzez określenie [filtrów na lokalizatora przesyłania strumieniowego](filters-concept.md#associating-filters-with-streaming-locator) + dodatkowe urządzenia określone filtry określające przez klienta w adresie URL. Może to być przydatne do ograniczania dodatkowych ścieżek, takich jak strumienie metadanych lub zdarzenia, audio, języki lub opisu ścieżki audio. 

Możliwość określenia różnych filtrów na strumień, zapewnia zaawansowany **manifestów dynamicznych** manipulowania rozwiązania pod kątem wielu scenariuszy przypadków użycia dla urządzeń docelowych. W tym temacie opisano pojęcia związane z **manifestów dynamicznych** i zawiera przykładowe scenariusze, w których warto korzystać z tej funkcji.

> [!NOTE]
> Nie zmieniaj manifestów dynamicznych, elementu zawartości i domyślny manifest dla tego zasobu. 
> 

##  <a name="overview-of-manifests"></a>Omówienie manifestów

Usługa Azure Media Services obsługuje protokoły HLS, MPEG DASH i Smooth Streaming. Jako część [funkcję dynamicznego tworzenia pakietów](dynamic-packaging-overview.md), przesyłania strumieniowego manifesty na kliencie (listy odtwarzania wzorca HLS, DASH Media prezentacji opis [MPD] i Smooth Streaming) generowanych dynamicznie w oparciu o wybór formatu w adresie URL. Zobacz protokoły dostarczania w [Typowy przepływ pracy na żądanie](dynamic-packaging-overview.md#delivery-protocols). 

### <a name="get-and-examine-manifest-files"></a>Pobierz i sprawdź plik manifestu

Należy określić listę filtrów śledzenie właściwości warunków, które ścieżki strumienia (na żywo lub wideo na żądanie [wideo]) powinny być uwzględnione w manifeście utworzony dynamicznie w oparciu. Aby uzyskać i zbadać właściwości ścieżki, musisz najpierw załadować manifestu Smooth Streaming.

[Przekazywanie, kodowanie i przesyłanie strumieniowe plików przy użyciu platformy .NET](stream-files-tutorial-with-api.md#get-streaming-urls) samouczka dowiesz się, jak tworzyć adresy URL przesyłania strumieniowego przy użyciu platformy .NET. Po uruchomieniu aplikacji, wskazuje jeden z adresów URL w manifeście Smooth Streaming: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/> Skopiuj i wklej adres URL w pasku adresu przeglądarki. Plik zostanie pobrany. Możesz otworzyć go w wybranym edytorze tekstów.

Na przykład REST, zobacz [przekazywanie, kodowanie i przesyłanie strumieniowe plików z użyciem usług REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitor szybkości transmisji bitów strumienia wideo

Możesz użyć [strony pokaz usługi Azure Media Player](http://aka.ms/azuremediaplayer) monitorowanie szybkości transmisji bitów strumienia wideo. Na stronie wersji demonstracyjnej są wyświetlane informacje diagnostyczne na **diagnostyki** karty:

![Diagnostyka Azure Media Player][amp_diagnostics]
 
### <a name="examples-urls-with-filters-in-query-string"></a>Przykłady: Adresy URL z filtrami w ciągu zapytania

Filtry można stosować do ABR, Adaptive Bitrate protokołów przesyłania strumieniowego: HLS, MPEG-DASH i Smooth Streaming. W poniższej tabeli przedstawiono przykładowe adresy URL przy użyciu filtrów:

|Protocol|Przykład|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Filtrowania wyświetlania

Można zakodować element zawartości do wielu profilów kodowania (H.264 linii bazowej, H.264 wysoki, AACL, AACH, Dolby Digital Plus) i wielokrotnych jakości. Jednak nie wszystkie urządzenia klienta będzie obsługiwać profilów wszystkich elementów zawartości i szybkości transmisji. Na przykład starsze urządzenia z systemem Android obsługuje tylko H.264 linii bazowej + AACL. Wysyłanie wyższe szybkości transmisji do urządzenia, które nie mogą korzystać z zalet marnuje obliczenia przepustowości i urządzeń. Takie urządzenia muszą dekodowania wszystkie dane informacje tylko do skalowania w dół do wyświetlenia.

Manifestów dynamicznych, umożliwia tworzenie profilów urządzeń (takich jak aplikacje mobilne, konsoli lub HD/SD) i obejmuje ścieżek i klas, które mają być częścią każdego profilu. Jest to filtrowania wyświetlania. Na poniższym diagramie przedstawiono przykład go.

![Przykład filtrowania wyświetlania][renditions2]

W poniższym przykładzie koder użytego do kodowania zasobów mezzanine do siedmiu odwzorowaniami wideo każdego pliku MP4 z ISO (z 180p, aby 1080p). Zakodowanym elementem zawartości może być [dynamicznie spakowanych](dynamic-packaging-overview.md) do żadnego z następujących protokołów: HLS, MPEG DASH i Smooth. 

Górnej Poniższy diagram przedstawia HLS manifestu dla zasobu bez filtrów. (Zawiera wszystkie wersje siedem).  W lewym dolnym rogu na diagramie przedstawiono manifest HLS, do którego zastosowano filtr o nazwie "ott". Filtr "ott" Określa usunięcie wszystkich szybkości transmisji poniżej 1 MB/s, więc dwa poziomy jakości dolnej zostały usunięta, a w odpowiedzi. W prawym dolnym rogu na diagramie przedstawiono manifest HLS, do którego zastosowano filtr o nazwie "przenośnych". Filtr "przenośnych" oznacza usunięcie wersji, w którym rozwiązania jest większy niż 720p, więc dwóch 1080 p odwzorowaniami zostały usunięta, a.

![Filtrowania wyświetlania][renditions1]

## <a name="removing-language-tracks"></a>Usunięcie wersji językowych
Twoje zasoby mogą obejmować wiele języków audio, takich jak angielski, hiszpański, francuski, itp. Zazwyczaj menedżerów Player SDK domyślny wybór ścieżki audio i dostępne audio śledzi na wybór użytkownika.

Tworzenie takich zestawów SDK Player może być trudne, ponieważ wymaga to różne implementacje dla architektury odtwarzaczy specyficznych dla urządzenia. Ponadto na niektórych platformach Player interfejsy API są ograniczone i nie obejmują funkcji wyboru audio, gdzie użytkownicy nie mogą wybrać ani zmienić domyślną ścieżkę audio. Za pomocą filtrów zawartości można kontrolować zachowanie, tworząc filtry, które są uwzględnione jedynie żądane języki audio.

![Filtrowanie ścieżek języka][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Przycinanie początek elementu zawartości

W większości wydarzeń transmisji strumieniowej na żywo Operatorzy uruchomić niektóre testy przed rzeczywistego zdarzenia. Na przykład może być obejmują one plansz następująco przed rozpoczęciem zdarzenia: "Program rozpocznie chwilowo." 

Jeśli program jest archiwizacji, testów i planszy danych również są archiwizowane i w prezentacji. Jednak te informacje nie być wyświetlane na klientach. Za pomocą manifestów dynamicznych możesz utworzyć filtr godziny rozpoczęcia i usuwanie niepożądanych danych z manifestu.

![Przycinanie start][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Tworzenie subclips (widoki) z archiwum na żywo

Wiele wydarzeń na żywo są długo działające i archiwum na żywo może zawierać wiele zdarzeń. Po zakończeniu wydarzenia na żywo, zastosowań studyjnych warto podzielić dynamiczne archiwum na uruchamianie programu logicznych i Zatrzymaj sekwencji. 

Te programy wirtualnego można opublikować oddzielnie, bez wpis przetwarzania na żywo archiwum i nie są tworzone oddzielne zasoby (co nie możesz korzystać z istniejącej pamięci podręcznej fragmentów w sieci CDN). Przykładami takich wirtualnych programów są kwartałów football lub przeciwna gry, innings w mecz lub poszczególne zdarzenia programu sportu.

Za pomocą manifestów dynamicznych możesz tworzenie filtrów za pomocą godzin rozpoczęcia/zakończenia i tworzyć widoki wirtualnego w górnej części archiwum na żywo. 

![Filtr klipu podrzędnego][subclip_filter]

Poniżej przedstawiono przefiltrowane zasobów:

![Nartach][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Dostosowywanie okna prezentacji (DVR)

Obecnie usługa Azure Media Services oferuje cykliczne archiwum, w którym można skonfigurować czas trwania od 5 minut do 25 godzin. Filtrowanie manifestu można utworzyć stopniowe okna funkcji DVR w górnej części archiwum, bez usuwania nośnika. Istnieje wiele scenariuszy, w którym chcesz udostępnić ograniczone okno DVR, aby przenieść do krawędzi na żywo i jednocześnie zachować większe okna archiwizacji nadawców. Nadawca może wystąpić potrzeba użycia danych znajdujących się poza okna DVR, aby wyróżnić klipy lub chcą zapewniają różne DVR systemu windows na różnych urządzeniach. Na przykład większość urządzeń przenośnych nie obsługują duże okna DVR (użytkownik ma 2-minutowy okno DVR dla urządzeń przenośnych i godzinę dla klientów komputerów stacjonarnych).

![Okno DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Dostosowywanie LiveBackoff (transmisji na żywo)

Filtrowanie manifestu może służyć do usuwania kilka sekund na żywo krawędzi program na żywo. Filtrowanie dopuszcza nadawców, Obejrzyj prezentację na punkcie publikacji (wersja zapoznawcza) i utworzyć anons wstawiania punkty zanim widzów odbierać strumienia (wspierana przez 30 sekund). Zastosowań studyjnych następnie wypychać te anonse do ich struktur klienta w czasie ich do odbierania i przetwarzania informacji przed możliwości anonsu.

Oprócz obsługi anons można na żywo ustawienie wycofań dostosować położenie widzów tak, aby gdy klienci odstępstw i trafień na żywo krawędzi, mogą nadal uzyskiwać fragmentów z serwera. Dzięki temu klienci nie będą otrzymywać 412 Błąd lub błąd HTTP 404.

![Filtr na żywo wycofań][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Łączenie wielu reguł w jeden filtr

Można połączyć wiele reguł filtrowania w jeden filtr. Na przykład można zdefiniować "reguła zakresu" Aby usunąć plansz z archiwum na żywo i również odfiltrować różnych dostępnych. Gdy w przypadku stosowania wielu reguł filtrowania, efekt jest wspólną wszystkich reguł.

![Wiele reguł filtrowania][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Łączenie wielu filtrów (skład filtru)

Można także połączyć wiele filtrów w pojedynczego adresu URL. Następujący scenariusz pokazuje, dlaczego warto połączyć filtrów:

1. Należy filtrować swoje jakość wideo dla urządzeń przenośnych, takich jak Android i urządzenie iPad (w celu ograniczenia jakość wideo). Aby usunąć niechciane jakość, należy utworzyć filtr konta odpowiednie dla profilów urządzeń. Za pomocą filtrów kont dla wszystkich zasobów w ramach tego samego konta usługi Media Services bez dalszej skojarzenia.
1. Chcesz także trim godzina rozpoczęcia i zakończenia elementu zawartości. Aby to osiągnąć, możesz utworzyć filtr zasobów i ustawić czas rozpoczęcia i zakończenia. 
1. Chcesz połączyć oba te filtry. Bez kombinacji musisz dodać filtrowanie jakości, aby filtrować przycinania, co spowoduje, że użycie filtru trudniejsze.


Aby połączyć filtry, musisz ustawić nazw filtrów do manifestu/odtwarzania adresu URL w formacie rozdzielone średnikami. Załóżmy, że masz filtr o nazwie *MyMobileDevice* która filtruje jakość, a inny o nazwie *MyStartTime* można ustawić określony czas rozpoczęcia. Można połączyć maksymalnie trzy filtry. 

Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Istotne zagadnienia i ograniczenia

- Wartości **forceEndTimestamp**, **presentationWindowDuration**, i **liveBackoffDuration** nie powinna być ustawiona dla filtru wideo na żądanie. Są one używane tylko na potrzeby scenariuszy filtru na żywo. 
-  Dynamiczne manifest działa w granicach GOP (klatek kluczowych), więc przycinania ma GOP dokładności.
-  Można użyć dla tej samej nazwy filtru filtry kont i zasobów. Filtry zasobów mają wyższy priorytet i spowoduje przesłonięcie filtrów kont.
- Jeśli zaktualizujesz filtru, może potrwać do 2 minut, zanim punkt końcowy przesyłania strumieniowego odświeżyć zasady. Filtry są używane do udostępniania zawartości (buforowane zawartości serwerów proxy i usługa CDN buforuje), aktualizowanie te filtry może spowodować błędy odtwarzacza. Firma Microsoft zaleca, wyczyść pamięć podręczną po zaktualizowaniu filtru. Jeśli ta opcja nie jest możliwe, należy wziąć pod uwagę przy użyciu innego filtru.
- Klienci musieli ręcznie Pobierz manifest i przeanalizować sygnatura czasowa rozpoczęcia dokładne i Skala czasu.
    
    - Aby określić właściwości ścieżki w zasobie [get i zapoznaj się z plikiem manifestu](#get-and-examine-manifest-files).
    - Można ustawić właściwości sygnatury czasowej filtru zasobów jest: <br/>startTimestamp = &lt;godzina rozpoczęcia w manifeście&gt; +  &lt;oczekiwany czas rozpoczęcia filtrowania w ciągu kilku sekund&gt; * Skala czasu

## <a name="next-steps"></a>Następne kroki

Następujące artykuły pokazują, jak programowo utworzyć filtry:  

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
