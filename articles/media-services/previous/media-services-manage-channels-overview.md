---
title: Omówienie transmisji strumieniowej na żywo za pomocą usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie transmisji strumieniowych na żywo za pomocą usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: c5dd0146fe59e7dc85787f146b10cfde7d6addb4
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867897"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Omówienie transmisji strumieniowej na żywo za pomocą usługi Media Services

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

## <a name="overview"></a>Omówienie

Gdy dostarczanie wydarzeń transmisji strumieniowej na żywo w usłudze Azure Media Services to zwykle wymagane są następujące składniki:

* Kamera, która umożliwia emisję wydarzenia.
* Koder wideo na żywo, który konwertuje sygnały z kamery na strumienie wysyłane do usługi transmisji strumieniowej na żywo.

    Opcjonalnie wiele zsynchronizowanych czasowo koderów na żywo. W przypadku niektórych wydarzeń na żywo o kluczowym znaczeniu, które wymagają bardzo wysokiej dostępności i jakości obsługi, zaleca się wdrożenie nadmiarowych koderów w trybie aktywny/aktywny z synchronizacją czasu w celu uzyskania płynnego trybu failover bez utraty danych.
* Usługa transmisji strumieniowej na żywo, która umożliwia wykonanie następujących czynności:

  * pozyskiwanie zawartości na żywo przy użyciu różnych protokołów transmisji strumieniowej na żywo (np. RTMP lub Smooth Streaming);
  * (opcjonalnie) kodowanie strumienia do strumienia o adaptacyjnej szybkości transmisji bitów;
  * wyświetlanie podglądu transmisji strumieniowej na żywo;
  * rejestrowanie i przechowywanie pozyskiwanej zawartości w celu późniejszego przesłania jej strumieniowo (wideo na żądanie);
  * dostarczanie zawartości za pośrednictwem wspólnych protokołów przesyłania strumieniowego (np. MPEG DASH, Smooth, HLS) bezpośrednio do klientów lub do usługi Content Delivery Network (CDN) w celu dalszej dystrybucji.

Usługa **Microsoft Azure Media Services** (AMS) zapewnia możliwość pozyskiwania, kodowania, podglądu, przechowywania i dostarczania transmisji strumieniowej zawartości na żywo.

Za pomocą usługi Media Services, możesz korzystać z zalet [funkcję dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md), co pozwala na emisji strumieni na żywo w MPEG DASH, HLS, i Smooth Streaming formatuje z udziału, źródła danych jest wysyłanych do usługi. Przeglądającym można odtwarzać transmisji strumieniowej na żywo za pomocą dowolnego odtwarzaczy zgodne HLS, DASH lub Smooth Streaming. Usługa Azure Media Player w sieci web lub aplikacji mobilnych umożliwia dostarczanie strumienia we wszystkich tych protokołów.

> [!NOTE]
> Od 12 maja 2018 r. Usługa kanały na żywo będzie już obsługę strumienia transportu RTP/MPEG-2 protokołu pozyskiwania. Przeprowadź migrację z protokołu RTP/MPEG-2 do protokołu RTMP lub plików MP4 (Smooth Streaming) protokołów pozyskiwania.

## <a name="streaming-endpoints-channels-programs"></a>Przesyłanie strumieniowe punktów końcowych, kanałów i programów

W usłudze Azure Media Services **kanały**, **programy**, i **punkty końcowe przesyłania strumieniowego** obsługują wszystkie funkcje transmisji strumieniowej na żywo, w tym pozyskiwanie, formatowanie, cyfrowy rejestrator wideo (DVR), zabezpieczenia, skalowalność i nadmiarowość.

**Kanał** reprezentuje potok przetwarzania zawartości transmisji strumieniowej na żywo. Kanał może odbierać przychodzące strumienie na żywo w następujący sposób:

* Lokalny koder na żywo wysyła plik **RTMP** o różnych szybkościach transmisji bitów lub plik **Smooth Streaming** (pofragmentowany plik MP4) do kanału, który jest skonfigurowany do obsługi dostarczania zawartości w formie **przekazywania**. Dostarczanie w formie **przekazywania** występuje wtedy, gdy pozyskiwane strumienie są przekazywane przez **kanał** bez dalszego przetwarzania. Można użyć następujących koderów na żywo, które zwracają Smooth Streaming o różnych szybkościach transmisji bitów: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco i Elemental. Następujące kodery na żywo danych wyjściowych RTMP: Adobe Flash Media Live Encoder (FMLE), Telestream Wirecast, Haivision, Teradek i Transkodery tricaster.  Koder na żywo może także wysłać strumień o pojedynczej szybkości transmisji bitów do kanału, który nie obsługuje kodowania na żywo, nie jest to jednak zalecane. Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

  > [!NOTE]
  > Metoda przekazywania to najbardziej ekonomiczne rozwiązanie transmisji strumieniowej na żywo w przypadku organizowania wielu wydarzeń w długim okresie oraz poczynionych inwestycji w kodery lokalne. Zobacz szczegółowe informacje o [cenach](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* Lokalny koder na żywo wysyła strumień o pojedynczej szybkości bitów do kanału, który jest skonfigurowany do przeprowadzania kodowania na żywo z usługą Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (pofragmentowany MP4). Następujące kodery na żywo z danymi wyjściowymi RTMP znane pracować z kanałami tego typu: Telestream Wirecast FMLE. Kanał wykonuje następnie kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do postaci strumienia wideo o różnych szybkościach transmisji bitów (adaptacyjnej szybkości transmisji bitów). Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

Począwszy od Media Services w wersji 2.10 wersji, podczas tworzenia kanału, można określić w jaki sposób chcesz kanału w taki sposób otrzymywać strumień wejściowy i określa, czy chcesz dla kanału do przeprowadzania kodowania na żywo strumienia. Dostępne są dwie opcje:

* **Brak** (przekazujących) — określ tę wartość, jeśli zamierzasz używać na lokalny koder na żywo, która zwróci strumień o różnych szybkościach transmisji bitów (strumień przekazującego). W tym przypadku przychodzącego strumienia przekazywane do dane wyjściowe bez żadnych kodowania. Jest to zachowanie kanału przed wydaniem 2.10.  
* **Standardowa** — wybierz tę wartość, jeśli planujesz używać usługi Media Services do zakodowania transmisji strumieniowej na żywo o pojedynczej szybkości transmisji bitów do strumienia o wielokrotnej szybkości transmisji bitów. Ta metoda jest bardziej ekonomiczny w przypadku skalowania w górę szybko rzadkie zdarzeń. Należy pamiętać, że istnieje rozliczeń wpływ kodowanie na żywo i należy pamiętać, że pozostawienie kodowania kanału na żywo w stanie "Uruchomiona" spowoduje naliczenie opłat rozliczeń.  Zaleca się natychmiast zatrzymać kanałów uruchomione po zakończeniu zdarzenia przesyłania strumieniowego na żywo, aby uniknąć dodatkowych stawki godzinowe.

## <a name="comparison-of-channel-types"></a>Porównanie typów kanałów

Poniższa tabela zawiera przewodnik do porównywania dwóch typów kanałów obsługiwane w usłudze Media Services

| Cecha | Kanału przekazującego | Wzorzec kanał |
| --- | --- | --- |
| Pojedyncza szybkość transmisji bitów w danych wejściowych jest zakodowany do wielokrotnych w chmurze |Nie |Yes |
| Maksymalna rozdzielczość, liczba warstw |1080p, 8 warstwy ponad 60 kl. / s |720p 6 warstwy 30 kl. / s |
| Protokoły danych wejściowych |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Cena |Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) i kliknij kartę "Wideo na żywo" |Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) |
| Maksymalny czas działania |Całodobowo |8 godzin |
| Obsługa wstawiania plansz |Nie |Yes |
| Obsługa sygnalizowanie ad |Nie |Yes |
| Podpisy napisy kodowane CEA 608/708 przekazywania |Yes |Yes |
| Obsługa niejednolitego GOPs danych wejściowych |Yes |Nie — dane wejściowe zostały poprawione 2 s GOPs |
| Obsługa dane wejściowe szybkość klatek zmiennej |Yes |Nie — dane wejściowe muszą zostać usunięte, szybkości klatek.<br/>Niewielkich zmian są dopuszczalne na przykład podczas sceny wysokiej ruchu. Ale kodera nie można porzucić do 10 ramek na sekundę. |
| Auto bliskie kanały, gdy dane wejściowe źródła danych zostaną utracone |Nie |Po upływie 12 godzin, jeśli nie jest uruchomiony żaden Program |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych (przekazujących)

Na poniższym diagramie przedstawiono główne elementy platformy AMS, które są zaangażowane w przepływ pracy **przekazywania**.

![Przepływ pracy na żywo](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Praca z kanałami obsługującymi kodowanie na żywo za pomocą usługi Azure Media Services

Na poniższym diagramie przedstawiono główne elementy platformy AMS biorące udział w przepływie pracy transmisji strumieniowej na żywo, gdy kanał obsługuje kodowanie na żywo za pomocą usługi Media Services.

![Przepływ pracy na żywo](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Opis kanału i jego składników powiązane

### <a name="channel"></a>Kanał

W usłudze Media Services [kanału](https://docs.microsoft.com/rest/api/media/operations/channel)s są zobowiązani do przetwarzania zawartości transmisji strumieniowej na żywo. Kanał oferuje wejściowy punkt końcowy (adres URL pozyskiwania) następnie podaj transkoder na żywo. Kanał odbiera strumieni danych wejściowych z transkoder na żywo i udostępnia go do przesyłania strumieniowego za pośrednictwem co najmniej jeden punkty. Kanały zapewniają również punkt końcowy (wersja zapoznawcza) (adres URL w wersji zapoznawczej), która umożliwia podgląd i weryfikować strumień przed dalszym przetwarzaniem i dostarczaniem.

Po utworzeniu kanału można uzyskać adresu URL pozyskiwania i adres URL w wersji zapoznawczej. Aby uzyskać te adresy URL, kanał nie ma być w stanie uruchomionym. Gdy wszystko jest gotowe rozpocząć wypychanie danych z transkoder na żywo do kanału, kanał musi zostać uruchomiona. Po uruchomieniu na żywo transkoder dane wprowadzane można wyświetlać podgląd strumienia.

Każde konto usługi Media Services może zawierać wielu kanałów, wiele programów i wielu punkty. W zależności od potrzeb przepustowości i bezpieczeństwa usługi StreamingEndpoint mogą być przeznaczone do obsługi co najmniej jednego kanału. Wszelkie StreamingEndpoint ściągnąć z dowolnym kanału.

Podczas tworzenia kanału, można określić dozwolone adresy IP w jednym z następujących formatów: adres IPv4 z 4 cyframi, zakres adresów CIDR.

### <a name="program"></a>Program
A [Program](https://docs.microsoft.com/rest/api/media/operations/program) pozwala na kontrolowanie publikowania i przechowywania segmentów strumienia na żywo. Kanały zarządzają programami. Relacja kanału i programu jest bardzo podobna do relacji w tradycyjnych multimediach, gdzie kanał ma stały strumień zawartości, a program obejmuje niektóre zdarzenia czasowe na tym kanale.
Można określić liczbę godzin, aby zachować zarejestrowaną zawartość na potrzeby programu przez ustawienie **ArchiveWindowLength** właściwości. Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin.

ArchiveWindowLength również mówią, że maksymalną ilość czasu klientów można wyszukać w czasie od bieżącej pozycji na żywo. Programy mogą być transmitowane w określonym czasie, ale zawartość, która wykracza poza długość okna, jest stale odrzucana. Wartość tej właściwości określa również, jak długie mogą być manifesty na kliencie.

Każdy program jest skojarzony z zasobem. Aby opublikować program, należy utworzyć Lokalizator dla skojarzonego elementu zawartości. Lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy jednocześnie uruchomione programy, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Na przykład zgodnie z wymaganiami biznesowymi potrzebna jest archiwizacja sześciu godzin programu, ale do emisji przeznaczonych jest tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden z programów jest skonfigurowany na potrzeby archiwizacji sześciu godzin zdarzenia, ale ten program nie jest publikowany. Drugi z programów jest skonfigurowany w celu archiwizacji 10 minut wydarzenia i ten program zostanie opublikowany.

## <a name="billing-implications"></a>Opłaty wpływ
Kanał rozpoczyna rozliczeń zaraz po zmianie stanu na "Uruchomione" jest za pośrednictwem interfejsu API.  

W poniższej tabeli przedstawiono, jak Stany kanału mapują stanom rozliczeń w portalu usługi interfejsu API i platformy Azure. Należy pamiętać, że Stany różnią się nieco między komputerowy Portal i interfejsu API Jak najszybciej kanał jest w stanie "Uruchomiona", za pośrednictwem interfejsu API lub w stanie "Gotowy" lub "Przesyłanie strumieniowe" w witrynie Azure portal rozliczeń będzie aktywny.

Zatrzymanie kanału z rozliczeń można dalej, musisz zatrzymać kanał za pomocą interfejsu API lub w witrynie Azure portal.
Odpowiedzialność za zatrzymywanie kanałów, po wykonaniu kanale. Nie można zatrzymać kanał spowoduje dalsze rozliczeń.

> [!NOTE]
> Podczas pracy z kanały standardowe, AMS automatycznie bliskie dowolny kanał, który jest nadal w stanie "Uruchomiona" 12 godzin po źródła danych wejściowych zostaną utracone, a nie Brak programów, uruchamianie. Jednakże możesz nadal jest naliczana za czas, jaki kanał był w stanie "Uruchomiona".
>
>

### <a id="states"></a>Stany kanału oraz sposobu mapowania ich na tryb rozliczeń
Bieżący stan kanału. Możliwe wartości obejmują:

* **Zatrzymano**. To jest wstępny stan kanału po jego utworzeniu (chyba że autostart został wybrany w portalu). Rozliczenia nie występuje w tym stanie. W tym stanie właściwości kanału mogą być aktualizowane, ale transmisja strumieniowa jest niedozwolona.
* **Uruchamianie**. Kanał jest uruchamiany. Rozliczenia nie występuje w tym stanie. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa. Jeśli wystąpi błąd, kanał wróci do stanu Zatrzymany.
* **Uruchamianie**. Kanał może przetwarzać transmisje strumieniowe na żywo. Teraz jest to fakturowania użycia. Należy zatrzymać kanał, aby uniknąć dalszych rozliczeń.
* **Zatrzymywanie**. Kanał jest zatrzymywany. Rozliczenia nie występuje w tym stanie przejściowym. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.
* **Usuwanie**. Kanał jest usuwany. Rozliczenia nie występuje w tym stanie przejściowym. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.

W tabeli poniżej pokazano, jak stany kanału przekładają się na naliczanie opłat.

| Stan kanału | Wskaźniki w interfejsie użytkownika portalu | Jest to rozliczeń? |
| --- | --- | --- |
| Uruchamianie |Uruchamianie |Nie (stan przejściowy) |
| Działanie |Gotowy (brak uruchomionych programów)<br/>lub<br/>Transmisja strumieniowa (co najmniej jeden uruchomiony program) |TAK |
| Zatrzymywanie |Zatrzymywanie |Nie (stan przejściowy) |
| Zatrzymano |Zatrzymano |Nie |

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Powiązane tematy
[Specyfikacja odbierania podzielonej zawartości w formacie MP4 na żywo w usłudze Azure Media Services](media-services-fmp4-live-ingest-overview.md)

[Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych](media-services-live-streaming-with-onprem-encoders.md)

[Przydziały i ograniczenia](media-services-quotas-and-limitations.md).  

[Pojęcia dotyczące usługi Media Services](media-services-concepts.md)
