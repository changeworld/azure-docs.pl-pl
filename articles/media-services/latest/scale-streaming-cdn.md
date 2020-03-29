---
title: Przesyłanie strumieniowe zawartości za pomocą integracji z siecią CDN
titleSuffix: Azure Media Services
description: Dowiedz się więcej o przesyłaniu strumieniowego zawartości z integracją z siecią CDN, a także z dostępem do systemu prefching i z pomocą wstępną Origin-Assist.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: 4ed8ada306720b7a8b44ddd59cefe399238c906a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128066"
---
# <a name="stream-content-with-cdn-integration"></a>Przesyłanie strumieniowe zawartości za pomocą integracji z siecią CDN

Usługa Azure Content Delivery Network (CDN) oferuje deweloperom globalne rozwiązanie umożliwiające szybkie dostarczanie użytkownikom zawartości wymagającej wysokiej przepustowości przez zapisywanie zawartości w pamięci podręcznej w węzłach fizycznych strategicznie rozmieszczonych na całym świecie.  

Usługa CDN buforuje zawartość przesyłaną strumieniowo z [punktu końcowego przesyłania strumieniowego](streaming-endpoint-concept.md) usługi Media Services (pochodzenia) na kodek, na protokół przesyłania strumieniowego, szybkość transmisji bitów, format kontenera i na szyfrowanie/DRM. Dla każdej kombinacji kodek-streaming protokołu-kontener format-bitrate-encryption, będzie oddzielne pamięci podręcznej sieci CDN.

Popularna zawartość będzie obsługiwana bezpośrednio z pamięci podręcznej CDN, o ile fragment wideo jest buforowany. Zawartość na żywo może być buforowana, ponieważ zazwyczaj wiele osób ogląda dokładnie to samo. Zawartość na żądanie może być nieco trudniejsza, ponieważ możesz mieć niektóre treści, które są popularne, a niektóre nie. Jeśli masz miliony zasobów wideo, gdzie żaden z nich nie jest popularny (tylko jeden lub dwóch widzów w tygodniu), ale masz tysiące ludzi oglądających wszystkie różne filmy, CDN staje się znacznie mniej skuteczne.

Należy również wziąć pod uwagę, jak działa adaptacyjne przesyłanie strumieniowe. Każdy pojedynczy fragment wideo jest buforowany jako własną jednostkę. Wyobraźmy sobie na przykład, że po raz pierwszy oglądany jest określony film. Jeśli widz przeskakuje oglądając tylko kilka sekund tu i tam, tylko fragmenty wideo związane z tym, co osoba oglądana dostać w pamięci podręcznej w CDN. Dzięki adaptacyjnej transmisji strumieniowej zazwyczaj masz od 5 do 7 różnych bitów wideo. Jeśli jedna osoba obserwuje jedną szybkość transmisji bitów, a inna osoba ogląda inną szybkość transmisji bitów, każda z nich jest buforowana oddzielnie w sieci CDN. Nawet jeśli dwie osoby oglądają tę samą szybkość transmisji bitów, mogą przesyłać strumieniowo za różnych protokołów. Każdy protokół (HLS, MPEG-DASH, Smooth Streaming) jest buforowany oddzielnie. Tak więc każda szybkość transmisji bitów i protokół są buforowane oddzielnie i tylko te fragmenty wideo, które zostały wymagane są buforowane.

Przy podejmowaniu decyzji, czy włączyć sieć CDN w [punkcie końcowym przesyłania strumieniowego](streaming-endpoint-concept.md)usługi Media Services, należy wziąć pod uwagę liczbę przewidywanych widzów. Usługa CDN pomaga tylko wtedy, gdy oczekujesz wielu widzów za treści. Jeśli maksymalna współbieżność widzów jest niższa niż 500, zaleca się wyłączenie sieci CDN, ponieważ sieć CDN najlepiej skaluje się ze współbieżnością.

W tym temacie omówiono włączanie [integracji sieci CDN](#enable-azure-cdn-integration). Wyjaśniono również wstępnefching (aktywne buforowanie) i [origin-assist CDN-Prefetch](#origin-assist-cdn-prefetch) koncepcji.

## <a name="considerations"></a>Zagadnienia do rozważenia

* [Punkt](streaming-endpoint-concept.md) `hostname` końcowy przesyłania strumieniowego i adres URL przesyłania strumieniowego pozostają takie same, niezależnie od tego, czy włączysz sieć CDN.
* Jeśli potrzebujesz możliwości testowania zawartości z siecią CDN lub bez niej, utwórz inny punkt końcowy przesyłania strumieniowego, który nie jest włączony.

## <a name="enable-azure-cdn-integration"></a>Włączanie integracji usługi Azure CDN

> [!IMPORTANT]
> Nie można włączyć usługi CDN dla kont platformy próbnej lub studenckiej platformy Azure.
>
> Integracja usługi CDN jest włączona we wszystkich centrach danych platformy Azure z wyjątkiem regionów rządu federalnego i Chin.

Po zakończeniu przesyłania strumieniowego jest aprowizowana z włączoną siecią CDN, istnieje zdefiniowany czas oczekiwania w usłudze Media Services przed wykonaniem aktualizacji DNS w celu mapowania punktu końcowego przesyłania strumieniowego do punktu końcowego usługi CDN.

Jeśli później chcesz wyłączyć/włączyć usługę CDN, punkt końcowy przesyłania strumieniowego musi być w stanie **zatrzymania.** Włączenie integracji usługi Azure w sieci CDN może potrwać do dwóch godzin, a zmiany będą aktywne we wszystkich usługach YKP usługi CDN. Można jednak uruchomić punkt końcowy przesyłania strumieniowego i przesyłać strumieniowo bez przerw z punktu końcowego przesyłania strumieniowego. Po zakończeniu integracji strumień jest dostarczany z sieci CDN. W okresie inicjowania obsługi administracyjnej punkt końcowy przesyłania strumieniowego będzie w stanie **początkowym** i można zaobserwować obniżoną wydajność.

Po utworzeniu standardowego punktu końcowego przesyłania strumieniowego jest on domyślnie skonfigurowany w standardzie Verizon. Dostawców Premium Verizon lub Standard Akamai można skonfigurować przy użyciu interfejsów API REST.

Integracja usługi Azure Media Services z usługą Azure CDN jest implementowana w **usłudze Azure CDN firmy Verizon** dla standardowych punktów końcowych przesyłania strumieniowego. Punkty końcowe przesyłania strumieniowego w warstwie Premium można skonfigurować przy użyciu wszystkich **warstw i dostawców usług Azure CDN.**

> [!NOTE]
> Aby uzyskać szczegółowe informacje o usłudze Azure CDN, zobacz [omówienie usługi CDN](../../cdn/cdn-overview.md).

## <a name="determine-if-a-dns-change-was-made"></a>Określanie, czy nastąpiła zmiana dns

Można określić, czy zmiana dns została wystosowana w punkcie końcowym przesyłania <https://www.digwebinterface.com>strumieniowego (ruch jest kierowany do usługi Azure CDN) za pomocą . Jeśli widzisz azureedge.net nazw domen w wynikach, ruch jest teraz wskazywany na sieć CDN.

## <a name="origin-assist-cdn-prefetch"></a>Punkt z asystentem początkowym CDN-Prefetch

Buforowanie CDN jest procesem reaktywnym. Jeśli usługa CDN może przewidzieć, jaki zostanie wymagany następny obiekt, usługa CDN może aktywnie żądać i buforować następny obiekt. Za pomocą tego procesu można osiągnąć cache-hit dla wszystkich (lub większość) obiektów, co zwiększa wydajność.

Koncepcja wstępnego naciągania dąży do umieszczenia obiektów na "skraju internetu" w oczekiwaniu na to, że zostaną one wkrótce zamówione przez gracza, skracając w ten sposób czas dostarczenia tego przedmiotu graczowi.

Aby osiągnąć ten cel, punkt końcowy przesyłania strumieniowego (pochodzenie) i cdn muszą pracować ręka w rękę na kilka sposobów:

- Źródło usługi Media Services musi mieć "analizy" (Origin-Assist), aby poinformować cdn następnego obiektu do wstępnego wciąkania.
- CDN wykonuje ziszczenie wstępne i buforowanie (część CDN-prefetch). CDN musi również mieć "inteligencję", aby poinformować pochodzenie, czy jest to pobieranie z góry, czy regularne pobieranie, obsługa odpowiedzi 404 i sposób na uniknięcie niekończącej się pętli pobierania z prefetch.

### <a name="benefits"></a>Korzyści

Zalety funkcji *Origin-Assist CDN-Prefetch* obejmują:

- Pobieranie wstępne poprawia jakość odtwarzania wideo dzięki wstępnemu pozycjonowaniu przewidywanych segmentów wideo na krawędzi podczas odtwarzania, zmniejszając opóźnienia dla przeglądarki i skracając czas pobierania segmentu wideo. Powoduje to krótszy czas uruchamiania wideo i niższe wystąpienia ponownego zabufferingu.
- Ta koncepcja ma zastosowanie do ogólnego scenariusza pochodzenia sieci CDN i nie ogranicza się do nośnika.
- Akamai dodał tę funkcję do [Akamai Cloud Embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html).

> [!NOTE]
> Ta funkcja nie ma jeszcze zastosowania do akamai CDN zintegrowanej z punktem końcowym przesyłania strumieniowego usługi Media Services. Jest jednak dostępna dla klientów usługi Media Services, którzy mają wcześniej istniejącą umowę Akamai i wymagają niestandardowej integracji między siecią CDN Akamai a źródłem usługi Media Services.

### <a name="how-it-works"></a>Jak to działa

Obsługa sieci CDN dla `Origin-Assist CDN-Prefetch` nagłówków (zarówno do przesyłania strumieniowego na żywo, jak i wideo na żądanie) jest dostępna dla klientów, którzy mają bezpośredni kontrakt z Akamai CDN. Funkcja obejmuje następujące wymiany nagłówków HTTP między Akamai CDN i źródłem usługi Media Services:

|Nagłówek HTTP|Wartości|Nadawca|Odbiornik|Przeznaczenie|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1 (domyślnie) lub 0 |CDN|Origin|Aby wskazać, że sieć CDN jest włączona z dostępem do ziwy.|
|`CDN-Origin-Assist-Prefetch-Path`| Przykład: <br/>Fragmenty(wideo=1400000000,format=mpd-time-cmaf)|Origin|CDN|Aby zapewnić ścieżkę z przedsprzedażową do sieci CDN.|
|`CDN-Origin-Assist-Prefetch-Request`|1 (żądanie z góry) lub 0 (zwykłe żądanie)|CDN|Origin|Aby wskazać żądanie z sieci CDN jest zwężeniem z góry.|

Aby wyświetlić część wymiany nagłówka w akcji, możesz wykonać następujące czynności:

1. Użyj listonosza lub cURL, aby wysłać żądanie do źródła usługi Media Services dla segmentu lub fragmentu audio lub wideo. Upewnij się, aby `CDN-Origin-Assist-Prefetch-Enabled: 1` dodać nagłówek w żądaniu.
2. W odpowiedzi powinien zostać wyświetlony `CDN-Origin-Assist-Prefetch-Path` nagłówek ze ścieżką względną jako jego wartość.

### <a name="supported-streaming-protocols"></a>Obsługiwane protokoły przesyłania strumieniowego

Ta `Origin-Assist CDN-Prefetch` funkcja obsługuje następujące protokoły przesyłania strumieniowego do przesyłania strumieniowego na żywo i na żądanie:

* HLS v3
* HLS v4
* HLS CMAF
* DASH (CSF)
* DASH (CMAF)
* Płynna transmisja strumieniowa

### <a name="faqs"></a>Często zadawane pytania

* Co zrobić, jeśli adres URL ścieżki z fragmentem wstępnym jest nieprawidłowy, dzięki czemu zdobycz z przedpremierowy cdn otrzymuje 404?

    Usługa CDN będzie buforować tylko odpowiedź 404 przez 10 sekund (lub inną skonfigurowaną wartość).

* Załóżmy, że masz film na żądanie. Jeśli funkcja z prefetch cdn jest włączona, czy ta funkcja oznacza, że gdy klient zażąda pierwszego segmentu wideo, zczep.

    Nie, cdn-prefetch odbywa się tylko po zainicjowane przez klienta żądanie/odpowiedź. Cdn-prefetch nigdy nie jest wyzwalany przez zwężone, aby uniknąć pętli z przedsprzedaży.

* Czy funkcja Origin-Assist CDN-Prefetch jest zawsze wskładka? Jak można go włączyć / wyłączyć?

    Ta funkcja jest domyślnie wyłączona. Klienci muszą go włączyć za pośrednictwem interfejsu API Akamai.

* Co by się stało z Origin-Assist, jeśli następny segment lub fragment nie jest jeszcze dostępny?

    W takim przypadku źródło usługi Media `CDN-Origin-Assist-Prefetch-Path` Services nie zapewni nagłówka i nie nastąpi dostęp do ziwy z wyprzedzeniem usługi CDN.

* Jak `Origin-Assist CDN-Prefetch` działa z dynamicznymi filtrami manifestów?

    Ta funkcja działa niezależnie od filtru manifestu. Gdy następny fragment jest poza oknem filtru, jego adres URL nadal będzie znajdować się, patrząc na manifest klienta pierwotnego, a następnie zwrócony jako nagłówek odpowiedzi z przedbrań sieci CDN. Więc CDN otrzyma adres URL fragmentu, który jest odfiltrowany z DASH/HLS/Smooth manifestu. Jednak odtwarzacz nigdy nie złoży żądania GET do cdn, aby pobrać ten fragment, ponieważ fragment ten nie jest uwzględniony w dash/HLS/Smooth manifestu posiadanych przez odtwarzacz (odtwarzacz nie wie, że fragment istnienia).

* Czy dash MPD / HLS listy odtwarzania / Smooth manifest być prefetched?

    Nie, DASH MPD, główna playlista HLS, playlista wariantu HLS lub gładki adres URL manifestu nie są dodawane do nagłówka z nadpremierowym.

* Czy adresy URL z nadpremierowe są względne lub bezwzględne?

    Podczas gdy Akamai CDN zezwala na oba, źródło usługi Media Services zapewnia tylko względne adresy URL dla ścieżki z wyprzedzeniem, ponieważ nie ma widocznych korzyści w użyciu bezwzględnych adresów URL.

* Czy ta funkcja działa z zawartością chroniona przez drm?

    Tak, ponieważ ta funkcja działa na poziomie HTTP, nie dekoduje ani nie analizuje żadnego segmentu/fragmentu. Nie obchodzi go, czy zawartość jest szyfrowana, czy nie.

* Czy ta funkcja działa z wstawianiem reklam po stronie serwera (SSAI)?
    
    To działa dla oryginalnej / głównej zawartości (oryginalna zawartość wideo przed wstawieniem reklamy) działa, ponieważ SSAI nie zmienia sygnatury czasowej treści źródłowej z źródła usługi Media Services. To, czy ta funkcja działa z zawartością reklamy, zależy od tego, czy źródło reklamy obsługuje Origin-Assist. Jeśli na przykład zawartość reklamy jest również hostowana w usłudze Azure Media Services (tego samego lub innego pochodzenia), zawartość reklamy również zostanie naliczona z wyprzedzeniem.

* Czy ta funkcja działa z zawartością UHD/HEVC?

    Tak.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="next-steps"></a>Następne kroki

* Upewnij się, że przejrzyj dokument [punktu końcowego przesyłania strumieniowego (pochodzenia).](streaming-endpoint-concept.md)
* Przykład [w tym repozytorium](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) pokazuje, jak uruchomić domyślny punkt końcowy przesyłania strumieniowego z .NET.
