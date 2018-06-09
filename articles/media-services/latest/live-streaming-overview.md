---
title: Omówienie transmisji strumieniowej na żywo przy użyciu usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Dzięki temu temacie omówienie live streaming przy użyciu usługi Azure Media Services w wersji 3.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: b8c9375d8ad915200cbc8b2e1a62979fd1b7d179
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238282"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Przesyłanie strumieniowe z usługi Azure Media Services w wersji 3 na żywo

Gdy dostarczanie wydarzeń transmisji strumieniowej na żywo usłudze Azure Media Services zwykle obejmuje następujące składniki:

* Kamera, która umożliwia emisję wydarzenia.
* Usługa transmisji strumieniowej na żywo koder wideo na żywo, który konwertuje sygnały z kamery (lub inne urządzenie, takie jak laptop) do strumieni, które są wysyłane do usługi Media Services. Sygnały mogą również obejmować anonsowanie SCTE 35 i wskaźników Ad. 
* Usługa Media Services transmisji strumieniowej na żywo umożliwia pozyskiwania, pakietu, rejestrować, szyfrowania i przeglądanie emisji zawartości do klientów lub CDN do dalszej dystrybucji.

Ten artykuł zawiera szczegółowe omówienie i zawiera diagramy główne składniki zaangażowane w transmisję strumieniową na żywo za pomocą usługi Media Services.

## <a name="overview-of-main-components"></a>Omówienie głównych składników

W usłudze Media Services [LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents) są zobowiązani do przetwarzania zawartości transmisji strumieniowej na żywo. LiveEvent zawiera ono wejściowy punkt końcowy (adres URL pozyskiwania) udzielić im na lokalny koder na żywo. LiveEvent odbiera strumienie wejściowe na żywo z kodera na żywo w formacie RTMP lub Smooth Streaming i udostępnia go do przesyłania strumieniowego za pośrednictwem jednego lub więcej [punkty końcowe](https://docs.microsoft.com/rest/api/media/streamingendpoints). A [LiveOutput](https://docs.microsoft.com/en-us/rest/api/media/liveoutputs) pozwala na kontrolowanie publikowania, rejestrowania i ustawienia okna DVR strumienia na żywo. LiveEvent także punktu końcowego podglądu (adres URL w wersji zapoznawczej), używanej do wyświetlania i weryfikowania strumienia przed dalszego przetwarzania i dostarczania. 

Usługa Media Services udostępnia **dynamicznego tworzenia pakietów**, która umożliwia podgląd i emisji zawartości w MPEG DASH, HLS, Smooth Streaming formatach transmisji strumieniowej bez konieczności ręcznie ponownie utworzyć pakiet w tych formatach. Można odtwarzać z dowolnego HLS, DASH lub Smooth odtwarzacza zgodne. Można również użyć [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) do przetestowania strumienia.

Usługa Media Services umożliwia dostarczanie zawartości dynamicznie zaszyfrowany (**szyfrowania dynamicznego**) z Advanced Encryption Standard (AES-128) lub jeden z trzech systemów zarządzania (prawami cyfrowymi DRM) głównych prawami cyfrowymi: Microsoft PlayReady Google Widevine i FairPlay firmy Apple. Usługa Media Services udostępnia usługę dostarczania kluczy AES i DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów.

W razie potrzeby można również zastosować **filtrowanie dynamiczne**, które mogą służyć do kontrolowania liczba ścieżek, formatów, szybkości transmisji bitów, które są wysyłane do zawodników. Usługa Media Services obsługuje również wstawiania reklam.


## <a name="liveevent-types"></a>Typy LiveEvent

A [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) może być jedna z dwóch typów: kodowanie na żywo i przekazywania. 

### <a name="live-encoding-with-media-services"></a>Live encoding w usłudze Media Services

![Kodowanie na żywo](./media/live-streaming/live-encoding.png)

Wysyła na lokalny koder na żywo o pojedynczej szybkości transmisji bitów LiveEvent, obsługujący kodowanie na żywo z usługi Media Services w jednym z następujących protokołów: RTMP lub Smooth Streaming (pofragmentowany MP4). LiveEvent wykonuje następnie kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do strumienia wideo o różnych szybkościach transmisji bitów (adaptacyjnej). Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

Podczas tworzenia tego typu LiveEvent należy określić **podstawowe** (LiveEventEncodingType.Basic).

### <a name="pass-through"></a>Przekazywanie

![Przekazywania](./media/live-streaming/pass-through.png)

Przekazywanego jest zoptymalizowana pod kątem strumienie na żywo długotrwałe lub 24 x 7 liniowej kodowanie na żywo przy użyciu na lokalny koder na żywo. Koder lokalnymi wysyła wielokrotnej szybkości transmisji bitów **RTMP** lub **Smooth Streaming** (pofragmentowany plik MP4) do LiveEvent, który jest skonfigurowany dla **przekazywanego** dostarczania. **Przekazywanego** jest dostarczana, gdy pozyskiwane strumienie są przekazywane za pośrednictwem **LiveEvent**s bez dalszego przetwarzania. 

LiveEvents przekazująca może obsługiwać się rozpoznawania 4K i HEVC przekazywać w przypadku użycia z Smooth Streaming protokołu pozyskiwania. 

Podczas tworzenia tego typu LiveEvent należy określić **Brak** (LiveEventEncodingType.None).

> [!NOTE]
> Metoda przekazywania to najbardziej ekonomiczne rozwiązanie transmisji strumieniowej na żywo w przypadku organizowania wielu wydarzeń w długim okresie oraz poczynionych inwestycji w kodery lokalne. Zobacz szczegółowe informacje o [cenach](https://azure.microsoft.com/pricing/details/media-services/).
> 

## <a name="liveevent-types-comparison"></a>Porównanie typów LiveEvent 

W poniższej tabeli porównano funkcje dwa typy LiveEvent.

| Cecha | LiveEvent przekazywania | Podstawowe LiveEvent |
| --- | --- | --- |
| Dane wejściowe pojedynczej szybkości transmisji bitów jest kodowane do wielokrotnych szybkości transmisji bitów w chmurze |Nie |Yes |
| Rozdzielczość maksymalna, liczba warstw |4Kp30  |720p 6 warstwy 30 klatek na sekundę |
| Protokoły wejściowych |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Cena |Zobacz [cennikiem](https://azure.microsoft.com/pricing/details/media-services/) i kliknij kartę "Wideo na żywo" |Zobacz [stronie dotyczącej cen](https://azure.microsoft.com/pricing/details/media-services/) |
| Maksymalny czas wykonywania |Całodobowo |Całodobowo |
| Obsługa Wstawianie typu |Nie |Yes |
| Obsługa dla reklamy sygnalizowania za pomocą interfejsu API|Nie |Yes |
| Obsługa dla reklamy sygnalizowania za pośrednictwem sieci wewnątrzpasmowej SCTE35|Yes |Yes |
| Podpisy CEA 608/708 przekazywania |Yes |Yes |
| Możliwość odzyskania z krótki wstrzymania w udziale źródła danych |Yes |Nie (LiveEvent rozpocznie slating sekund 6 + bez wprowadzania danych)|
| Obsługa niejednolitego GOPs wejściowych |Yes |Nie — dane wejściowe muszą być ustalone 2 s GOPs |
| Obsługa danych wejściowych szybkość zmiennej ramki |Yes |Nie — dane wejściowe muszą być ustalane szybkość klatek.<br/>Niewielkich zmian są dopuszczalne, na przykład podczas ruchu wysokiej sceny. Ale koder nie można porzucić do 10 klatek na sekundę. |
| Auto bliskie z LiveEvent podczas wprowadzania źródła danych zostaną utracone |Nie |Po 12 godzinach, jeśli nie istnieje żadne LiveOutput uruchomiona |

## <a name="liveevent-states"></a>Stany LiveEvent 

Bieżący stan LiveEvent. Możliwe wartości obejmują:

|Stan|Opis|
|---|---|
|**Zatrzymana**| Jest to początkowy stan LiveEvent po jego utworzeniu (chyba że wybrano automatyczne uruchamianie określonych.) Karta nie występuje w tym stanie. W tym stanie można zaktualizować właściwości LiveEvent, ale przesyłania strumieniowego nie jest dozwolone.|
|**Uruchamianie**| LiveEvent jest uruchamiana. Karta nie występuje w tym stanie. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa. Jeśli wystąpi błąd, LiveEvent powróci do stanu zatrzymania.|
|**Uruchomiona**| LiveEvent jest w stanie przetwarzania strumieni na żywo. Jest on teraz rozliczeń użycia. Musisz zatrzymać LiveEvent, aby uniemożliwić dalsze rozliczeń.|
|**Zatrzymywanie**| LiveEvent jest zatrzymywana. Karta nie występuje w tym stan przejściowy. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.|
|**Usuwanie**| Trwa usuwanie LiveEvent. Karta nie występuje w tym stan przejściowy. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.|

## <a name="liveoutput"></a>LiveOutput

A [LiveOutput](https://docs.microsoft.com/en-us/rest/api/media/liveoutputs) pozwala na kontrolowanie publikowania, rejestrowania i ustawienia okna DVR strumienia na żywo. Relacja LiveEvent i LiveOutput jest podobny do tradycyjnych multimediach, gdzie kanał (LiveEvent) ma stały strumień zawartości, a program (LiveOutput) obejmuje niektóre zdarzenia czasowe na tym LiveEvent.
Można określić liczbę godzin, które chcesz zachować zarejestrowaną zawartość dla LiveOutput przez ustawienie **ArchiveWindowLength** właściwości. **ArchiveWindowLength** jest czasu trwania ISO 8601 timespan długość okna archiwum (cyfrowy Rejestrator wideo lub DVR). Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin. 

**ArchiveWindowLength** również określa maksymalną liczbę klientów można wyszukiwać Wstecz w czasie z bieżącej pozycji na żywo. LiveOutputs można uruchomić w określonym czasie, ale zawartość, która wykracza poza długość okna jest stale odrzucana. Wartość tej właściwości określa również, jak długo mogą być manifesty klienta.

Każdy LiveOutput jest skojarzony z [zasobów](https://docs.microsoft.com/rest/api/media/assets)i zapisuje dane do kontenera w magazynie Azure powiązany z kontem usługi Media Services. Aby opublikować LiveOutput, należy utworzyć [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) dla skojarzonego elementu zawartości. Lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

LiveEvent obsługuje maksymalnie trzy jednocześnie uruchomione LiveOutputs, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Na przykład konkretnym wymaganiom biznesowym ma emisji 24 x 7 na żywo liniowej źródło danych, ale chcesz utworzyć "nagrań" programów w ciągu dnia, aby zaoferować klientom jako zawartości na żądanie w celu wyświetlenia wyrównującej.  W tym scenariuszu należy najpierw utworzyć podstawowego LiveOutput, z oknem archiwum krótkiej godziny lub mniej dla klientów dostroić jako podstawowy strumień na żywo. Czy utworzysz StreamingLocator dla tego LiveOutput i opublikujesz je do aplikacji lub witryny sieci web jako źródło "Live".  Jako źródła danych jest uruchomiona, programowego można utworzyć drugi LiveOutput równoczesnych na początku pokazu (lub podać niektóre uchwytów wczesne 5 minut później trim.) Ten drugi LiveOutput można zatrzymać 5 minut od zakończenia programu lub zdarzeń, a następnie możesz utworzyć nowe StreamingLocator, aby opublikować ten program jako element zawartości na żądanie w katalogu aplikacji.  Ten proces można powtarzać wiele razy dla innych granice programu lub wyróżnia się, że chcesz udostępnić jako na żądanie od razu, wszystkie podczas "Live" źródła danych z pierwszego LiveOutput w dalszym ciągu emisji liniowej źródła danych.  Ponadto może zająć obsługi filtru dynamicznego do obcięcia head i tail archiwum z LiveOutput, która wprowadzona dla "nakładają się na siebie bezpieczeństwa" między programami i osiągnąć dokładniejsze początek i koniec zawartości. Zarchiwizowaną zawartość może również zostać przesłane do [przekształcenie](https://docs.microsoft.com/rest/api/media/transforms) dla kodowania lub subclipping dokładne ramki do wielu formatów wyjściowych ma być używany jako zespolonego innych usług.

## <a name="streamingendpoint"></a>StreamingEndpoint

Po przesłaniu strumienia do LiveEvent, tworząc zasobów, LiveOutput i StreamingLocator można rozpocząć zdarzenie przesyłania strumieniowego. Spowoduje to archiwizację strumienia i udostępni go użytkownikom za pośrednictwem [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints).

Po utworzeniu konta usługi Media Services domyślnego punktu końcowego przesyłania strumieniowego jest dodany do Twojego konta w stanie zatrzymania. Aby rozpocząć przesyłanie strumieniowe zawartości i korzystać z dynamicznego tworzenia pakietów i dynamicznego szyfrowania, punktu końcowego przesyłania strumieniowego, z którego chcesz przesyłanie strumieniowe zawartości musi być w stan uruchomienia.

## <a name="billing"></a>Rozliczenia

LiveEvent rozpoczyna rozliczeń jak stanu przejścia do "Uruchomiona". Aby zatrzymać LiveEvent z rozliczeniami, należy zatrzymać LiveEvent.

> [!NOTE]
> Podczas **LiveEventEncodingType** na Twojej [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) ustawiono podstawowy, Media Services automatycznie bliskie żadnych LiveEvent, który jest nadal w stanie "Uruchomiona" 12 godzin po źródła danych wejściowych zostaną utracone, a nie Uruchomiona jest LiveOutputs. Jednak możesz będą nadal naliczane za czas, który LiveEvent był w stanie "Uruchomiona".
>

W poniższej tabeli przedstawiono sposób mapowania stanów LiveEvent do trybu rozliczeń.

| Stan LiveEvent | Jest to karta? |
| --- | --- |
| Uruchamianie |Nie (stan przejściowy) |
| Działanie |TAK |
| Zatrzymywanie |Nie (stan przejściowy) |
| Zatrzymano |Nie |

## <a name="next-steps"></a>Kolejne kroki

[Samouczek transmisji strumieniowej na żywo](stream-live-tutorial-with-api.md)
