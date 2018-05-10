---
title: Omówienie transmisji strumieniowej na żywo przy użyciu usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie transmisji strumieniowych na żywo przy użyciu usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: d71f9fc2b69e1a99b518c2317cccde712ab40fec
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="overview-of-live-streaming-using-azure-media-services"></a>Omówienie transmisji strumieniowej na żywo przy użyciu usługi Azure Media Services

> [!NOTE]
> Uruchamianie kanałów na żywo 12 maja 2018 będzie już obsługi strumienia transportowego RTP/MPEG-2 protokołu pozyskiwania. Przeprowadź migrację z RTP/MPEG-2 RTMP lub pofragmentowany MP4 (Smooth Streaming) pozyskiwania protokołów.

## <a name="overview"></a>Przegląd
Gdy dostarczanie wydarzeń transmisji strumieniowej na żywo usłudze Azure Media Services zwykle obejmuje następujące składniki:

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

Podczas dostarczania zawartości do klientów głównym celem jest przekazywanie zawartości wideo wysokiej jakości do różnych urządzeń bez względu na warunki panujące w sieci. Można to osiągnąć, użyj koderów na żywo w celu kodowania strumienia do strumienia wideo o różnych szybkościach transmisji bitów (adaptacyjnej szybkości transmisji bitów).  W celu obsługi przesyłania strumieniowego na różnych urządzeniach należy korzystać z funkcji [dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md) usługi Media Services, aby ponownie dynamicznie utworzyć pakiety na potrzeby transmisji strumieniowej do różnych protokołów. Usługa Media Services obsługuje dostarczanie następujących technologii przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów: HTTP Live Streaming (HLS), Smooth Streaming i MPEG DASH.

W usłudze Azure Media Services **kanały**, **programy**, i **punkty końcowe przesyłania strumieniowego** obsługują wszystkie funkcje transmisji strumieniowej na żywo, w tym pozyskiwanie, formatowanie, cyfrowy rejestrator wideo (DVR), zabezpieczenia, skalowalność i nadmiarowość.

**Kanał** reprezentuje potok przetwarzania zawartości transmisji strumieniowej na żywo. Kanał może odbierać przychodzące strumienie na żywo w następujący sposób:

* Lokalny koder na żywo wysyła plik **RTMP** o różnych szybkościach transmisji bitów lub plik **Smooth Streaming** (pofragmentowany plik MP4) do kanału, który jest skonfigurowany do obsługi dostarczania zawartości w formie **przekazywania**. Dostarczanie w formie **przekazywania** występuje wtedy, gdy pozyskiwane strumienie są przekazywane przez **kanał** bez dalszego przetwarzania. Można użyć następujących koderów na żywo, które udostępniają Smooth Streaming wielokrotnej szybkości transmisji bitów: MediaExcel, Ateme, Wyobraź sobie komunikacji, Envivio, Cisco i Elemental. Następujące kodery na żywo wysyłają pliki RTMP: Adobe Flash Media na żywo kodera (FMLE), Telestream Wirecast, Haivision, Teradek i transkodery Tricaster.  Koder na żywo może także wysłać strumień o pojedynczej szybkości transmisji bitów do kanału, który nie obsługuje kodowania na żywo, nie jest to jednak zalecane. Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

  > [!NOTE]
  > Metoda przekazywania to najbardziej ekonomiczne rozwiązanie transmisji strumieniowej na żywo w przypadku organizowania wielu wydarzeń w długim okresie oraz poczynionych inwestycji w kodery lokalne. Zobacz szczegółowe informacje o [cenach](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* Na lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do kanału, który jest skonfigurowany do przeprowadzania kodowania na żywo w usłudze Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (pofragmentowany MP4). RTP (MPEG-TS) jest również obsługiwany, pod warunkiem, że dedykowanego połączenia centrum danych Azure. Następujące kodery na żywo, przy czym dane wyjściowe RTMP wiadomo, że praca z kanałami tego typu: Telestream Wirecast, FMLE. Kanał wykonuje następnie kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do postaci strumienia wideo o różnych szybkościach transmisji bitów (adaptacyjnej szybkości transmisji bitów). Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

Począwszy od Media Services 2.10 wersji, podczas tworzenia kanału, można określić w jaki sposób chcesz dla kanału do odbierania strumienia wejściowego oraz czy ma dla kanału do przeprowadzania kodowania na żywo strumienia. Dostępne są dwie opcje:

* **Brak** (przekazujących) — podać tę wartość, jeśli planujesz używać na lokalny koder na żywo, którego dane wyjściowe obejmują wielokrotnej szybkości transmisji bitów stream (strumień przekazującego). W takim przypadku strumienia przychodzącego przekazywane do danych wyjściowych bez żadnych kodowania. Jest to zachowanie kanału przed 2.10 wersji.  
* **Standardowe** — wybierz tę wartość, jeśli planujesz używać usługi Media Services do kodowania transmisji strumieniowej na żywo pojedynczej szybkości transmisji bitów do strumienia o wielokrotnej szybkości transmisji bitów. Ta metoda jest bardziej ekonomiczne skalowania szybko rzadkim zdarzeń. Należy pamiętać, że istnieje rozliczeń wpływ kodowanie na żywo i należy pamiętać, że pozostawienie kanał kodowania na żywo w stanie "Uruchomiona" spowoduje naliczenie opłat rozliczeń.  Zaleca się natychmiast zatrzymać kanałów uruchomione po zakończeniu zdarzenia transmisji strumieniowej na żywo, aby zapobiec zmianom bardzo co godzinę.

## <a name="comparison-of-channel-types"></a>Porównanie typów kanałów
Poniższa tabela zawiera przewodnik dotyczący porównanie dwóch typów kanałów obsługiwane w usłudze Media Services

| Cecha | Kanał przekazywania | Wzorzec kanał |
| --- | --- | --- |
| Dane wejściowe pojedynczej szybkości transmisji bitów jest kodowane do wielokrotnych szybkości transmisji bitów w chmurze |Nie |Yes |
| Rozdzielczość maksymalna, liczba warstw |1080p, 8 warstwy 60 + kl. / s |720p 6 warstwy 30 klatek na sekundę |
| Protokoły wejściowych |RTMP, Smooth Streaming |RTMP, Smooth Streaming i RTP |
| Cena |Zobacz [cennikiem](https://azure.microsoft.com/pricing/details/media-services/) i kliknij kartę "Wideo na żywo" |Zobacz [stronie dotyczącej cen](https://azure.microsoft.com/pricing/details/media-services/) |
| Maksymalny czas wykonywania |Całodobowo |8 godzin |
| Obsługa Wstawianie typu |Nie |Yes |
| Obsługa sygnalizowania ad |Nie |Yes |
| Podpisy CEA 608/708 przekazywania |Yes |Yes |
| Możliwość odzyskania z krótki wstrzymania w udziale źródła danych |Yes |Nie (kanał rozpocznie slating sekund 6 + bez wprowadzania danych) |
| Obsługa niejednolitego GOPs wejściowych |Yes |Nie — dane wejściowe muszą być ustalone 2 s GOPs |
| Obsługa danych wejściowych szybkość zmiennej ramki |Yes |Nie — dane wejściowe muszą być ustalane szybkość klatek.<br/>Niewielkich zmian są dopuszczalne, na przykład podczas ruchu wysokiej sceny. Ale koder nie można porzucić do 10 klatek na sekundę. |
| Auto bliskie kanałów podczas wprowadzania źródła danych zostaną utracone |Nie |Po 12 godzinach, jeśli żaden Program uruchomiony |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych (przekazujących)
Na poniższym diagramie przedstawiono główne elementy platformy AMS, które są zaangażowane w przepływ pracy **przekazywania**.

![Przepływ pracy na żywo](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Praca z kanałami obsługującymi kodowanie na żywo za pomocą usługi Azure Media Services
Na poniższym diagramie przedstawiono główne elementy platformy AMS biorące udział w przepływie pracy transmisji strumieniowej na żywo, gdy kanał obsługuje kodowanie na żywo za pomocą usługi Media Services.

![Przepływ pracy na żywo](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Opis kanału i jej powiązane składniki
### <a name="channel"></a>Kanał
W usłudze Media Services [kanału](https://docs.microsoft.com/rest/api/media/operations/channel)s są zobowiązani do przetwarzania zawartości transmisji strumieniowej na żywo. Kanał zawiera ono wejściowy punkt końcowy (adres URL pozyskiwania) następnie podaj transcoder na żywo. Kanał odbiera strumienie wejściowe na żywo z transcoder na żywo i udostępnia go do przesyłania strumieniowego za pośrednictwem jednego lub więcej punkty końcowe. Kanały zapewniają również punkt końcowy w wersji zapoznawczej (adres URL w wersji zapoznawczej), używanej do wyświetlania i weryfikowania strumienia przed dalszego przetwarzania i dostarczania.

Po utworzeniu kanału możesz uzyskać adresu URL pozyskiwania i adres URL w wersji zapoznawczej. Aby uzyskać te adresy URL, kanał nie ma być w stanie uruchomienia. Gdy wszystko będzie gotowe uruchomić przekazywanie danych z transcoder na żywo do kanału, kanału musi być uruchomiona. Po uruchomieniu na żywo transcoder wprowadzania danych można wyświetlić podgląd strumienia.

Każde konto usługi Media Services może zawierać wiele kanałów, wiele programów i wielu punkty końcowe. W zależności od potrzeb przepustowości i zabezpieczeń StreamingEndpoint usług mogą być przeznaczone dla jednego lub kilku kanałów. Wszelkie StreamingEndpoint można ściąganie danych z dowolnego kanału.

### <a name="program"></a>Program
A [Program](https://docs.microsoft.com/rest/api/media/operations/program) pozwala na kontrolowanie publikowania i przechowywania segmentów strumienia na żywo. Kanały zarządzają programami. Relacja kanału i programu jest bardzo podobna do relacji w tradycyjnych multimediach, gdzie kanał ma stały strumień zawartości, a program obejmuje niektóre zdarzenia czasowe na tym kanale.
Można określić liczbę godzin, aby zachować zarejestrowaną zawartość na potrzeby programu przez ustawienie **ArchiveWindowLength** właściwości. Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin.

ArchiveWindowLength określa również dostępny maksymalną ilość czasu klientów można wyszukiwać wstecz od bieżącego położenia na żywo. Programy mogą być transmitowane w określonym czasie, ale zawartość, która wykracza poza długość okna, jest stale odrzucana. Wartość tej właściwości określa również, jak długie mogą być manifesty na kliencie.

Każdy program jest skojarzony z zasobem. Aby opublikować program musi utworzyć lokalizatora dla skojarzonego elementu zawartości. Lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy jednocześnie uruchomione programy, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Na przykład zgodnie z wymaganiami biznesowymi potrzebna jest archiwizacja sześciu godzin programu, ale do emisji przeznaczonych jest tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden z programów jest skonfigurowany na potrzeby archiwizacji sześciu godzin zdarzenia, ale ten program nie jest publikowany. Drugi z programów jest skonfigurowany w celu archiwizacji 10 minut wydarzenia i ten program zostanie opublikowany.

## <a name="billing-implications"></a>Implikacje rozliczeń
Kanał rozpoczyna rozliczeń jak przejść stanu do "Uruchomiona" jest za pośrednictwem interfejsu API.  

W poniższej tabeli przedstawiono sposób mapowania stanów kanału do stanów rozliczeń w portalu interfejsu API i usługi Azure. Należy zauważyć, że Stany różni się od interfejsu API i Portal UX. Jak najszybciej kanał jest w stanie "Uruchomiona" za pośrednictwem interfejsu API, lub w stanie "Gotowe" lub "Streaming" w portalu Azure, rozliczeń będzie aktywny.

Zatrzymał kanału z rozliczeniami można dodatkowo, należy zatrzymać kanału, za pomocą interfejsu API lub w portalu Azure.
Jest odpowiedzialny za zatrzymywanie kanałów, gdy wszystko będzie gotowe z kanałem. Nie można zatrzymać kanału spowoduje ciągłego rozliczeń.

> [!NOTE]
> Podczas pracy z kanałami standardowe, AMS automatycznie bliskie żadnych kanału, który jest nadal w stanie "Uruchomiona" 12 godzin po źródła danych wejściowych zostaną utracone i nie ma żadnych programów uruchomiony. Jednak możesz będą nadal naliczane za czas, który kanał był w stanie "Uruchomiona".
>
>

### <a id="states"></a>Stany kanału oraz sposobu mapowania ich na tryb rozliczeń
Bieżący stan kanału. Możliwe wartości obejmują:

* **Zatrzymano**. Jest to stan początkowy kanału po jego utworzeniu (chyba że automatyczne uruchamianie zostało wybrane w portalu). Karta nie występuje w tym stanie. W tym stanie właściwości kanału mogą być aktualizowane, ale transmisja strumieniowa jest niedozwolona.
* **Uruchamianie**. Kanał jest uruchamiany. Karta nie występuje w tym stanie. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa. Jeśli wystąpi błąd, kanał wróci do stanu Zatrzymany.
* **Uruchomiona**. Kanał może przetwarzać transmisje strumieniowe na żywo. Jest on teraz rozliczeń użycia. Musisz zatrzymać kanału, aby uniemożliwić dalsze rozliczeń.
* **Zatrzymywanie**. Kanał jest zatrzymywany. Karta nie występuje w tym stan przejściowy. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.
* **Usuwanie**. Kanał jest usuwany. Karta nie występuje w tym stan przejściowy. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.

W tabeli poniżej pokazano, jak stany kanału przekładają się na naliczanie opłat.

| Stan kanału | Wskaźniki w interfejsie użytkownika portalu | Jest to karta? |
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
[Specyfikacja pozyskiwania MP4 pofragmentowane na żywo w usłudze Azure Media Services](media-services-fmp4-live-ingest-overview.md)

[Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych](media-services-live-streaming-with-onprem-encoders.md)

[Przydziały i ograniczenia](media-services-quotas-and-limitations.md).  

[Pojęcia dotyczące usługi Media Services](media-services-concepts.md)
