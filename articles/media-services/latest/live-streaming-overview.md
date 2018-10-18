---
title: Omówienie transmisji strumieniowej na żywo za pomocą usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Dzięki temu tematu z omówieniem transmisja strumieniowa na żywo za pomocą usługi Azure Media Services v3.
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
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 533aa505c38d3cbfb46d70acecd43cc66614b13d
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378140"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Przesyłanie strumieniowe przy użyciu usługi Azure Media Services v3 na żywo

Gdy dostarczanie wydarzeń transmisji strumieniowej na żywo w usłudze Azure Media Services to zwykle wymagane są następujące składniki:

* Kamera, która umożliwia emisję wydarzenia.
* Koder wideo na żywo, który konwertuje sygnały z kamery (lub innego urządzenia, takie jak laptop) do strumieni, które są wysyłane do usługi przesyłania strumieniowego iąż. Sygnały mogą również obejmować anonsowanie SCTE 35 i podpowiedzi Ad. 
* Usługa Media Services transmisji strumieniowej na żywo umożliwia pozyskiwania, w wersji zapoznawczej, pakietów, rejestrowanie, szyfrowania i emisji zawartości do klientów lub do sieci CDN w celu dalszej dystrybucji.

W tym artykule szczegółowo omówiono i zawiera diagramy główne składniki zaangażowane w transmisji strumieniowej na żywo za pomocą usługi Media Services.

## <a name="overview-of-main-components"></a>Omówienie głównych składników

W usłudze Media Services kanały [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) są odpowiedzialne za przetwarzanie zawartości transmisji strumieniowej na żywo. Element LiveEvent zapewnia wejściowy punkt końcowy (adres URL pozyskiwania) następnie podaj lokalny koder na żywo. Element LiveEvent odbiera strumieni danych wejściowych z kodera na żywo w formacie RTMP lub Smooth Streaming i udostępnia go do przesyłania strumieniowego za pośrednictwem co najmniej jeden [punkty](https://docs.microsoft.com/rest/api/media/streamingendpoints). A [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) umożliwia kontrolowanie publikowania, zapisywania i funkcji DVR okno Ustawienia transmisji strumieniowej na żywo. Element LiveEvent jest także punktu końcowego (wersja zapoznawcza) (adres URL w wersji zapoznawczej), która umożliwia podgląd i weryfikować strumień przed dalszym przetwarzaniem i dostarczaniem. 

Usługa Media Services udostępnia **funkcję dynamicznego tworzenia pakietów**, co pozwala na przeglądanie i emisji zawartość w postaci strumieni MPEG DASH, HLS i Smooth Streaming formatach transmisji strumieniowej bez konieczności ręcznego ponownego tworzenia pakietów w tych formatach. Można odtwarzać za pomocą dowolnego HLS, DASH lub Smooth odtwarzacza zgodne. Można również użyć [usługi Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) do przetestowania strumienia.

Usługi Media Services umożliwia dostarczanie zawartości dynamicznie zaszyfrowany (**szyfrowania dynamicznego**) za pomocą Advanced Encryption Standard (AES-128) lub dowolnego systemu zarządzania (prawami cyfrowymi DRM) trzech głównych prawami cyfrowymi: Microsoft PlayReady Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów.

Jeśli to konieczne, można także zastosować **filtrowanie dynamiczne**, który może służyć do kontrolowania liczby ścieżek, formatów, szybkości transmisji, które są wysłane do odtwarzaczy. Usługa Media Services obsługuje także wstawianiem reklam.

### <a name="new-live-encoding-improvements"></a>Nowe ulepszenia kodowania na żywo

Następujące nowe ulepszenia zostały wykonane w najnowszej wersji.

- Nowy tryb małe opóźnienia na żywo (10 sekund end-to-end).
- Ulepszona obsługa protokołu RTMP (Zwiększona stabilność i więcej obsługę kodera źródłowego).
- Pozyskuj RTMPS bezpieczne.

    Po utworzeniu element LiveEvent teraz uzyskać adresy URL pozyskiwania 4. Pozyskiwanie 4 adresy URL są niemal identyczne, mają ten sam token przesyłania strumieniowego (AppId), tylko część numer portu jest inny. Są dwa adresy URL podstawowego i zapasowego dla RTMPS.   
- Obsługa transkodowanie 24-godzinnym. 
- Ulepszona obsługa sygnalizowanie ad w RTMP za pośrednictwem SCTE35.

## <a name="liveevent-types"></a>Element LiveEvent typów

A [element LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) może być jednym z dwóch typów: kodowanie na żywo oraz pass-through. 

### <a name="live-encoding-with-media-services"></a>Kodowanie za pomocą usługi Media Services na żywo

![funkcji Live encoding](./media/live-streaming/live-encoding.png)

Lokalny koder na żywo wysyła strumień o pojedynczej szybkości bitów na element LiveEvent, który jest skonfigurowany do przeprowadzania kodowania na żywo z usługą Media Services w jednym z następujących protokołów: RTMP lub Smooth Streaming (pofragmentowany MP4). Element LiveEvent wykonuje następnie kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do strumienia wideo różnych szybkościach transmisji bitów (adaptacyjnej). Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

Podczas tworzenia tego typu element LiveEvent należy określić **podstawowe** (LiveEventEncodingType.Basic).

### <a name="pass-through"></a>Przekazywanie

![przekazywane](./media/live-streaming/pass-through.png)

Przekazywane jest zoptymalizowany pod kątem transmisje strumieniowe na żywo długotrwałych lub 24 x 7 liniowej kodowania na żywo przy użyciu kodera na żywo w środowisku lokalnym. Lokalny koder wysyła różnych szybkościach transmisji bitów **RTMP** lub **Smooth Streaming** (pofragmentowany plik MP4) do element LiveEvent, który jest skonfigurowany dla **przekazywanego** dostarczania. **Przekazywanego** dostarczania jest, gdy pozyskiwane strumienie są przekazywane za pośrednictwem **element LiveEvent**bez dalszego przetwarzania. 

LiveEvents przekazująca może obsługiwać się 4K rozwiązania i — HEVC przechodzi przez, gdy jest używana przy użyciu funkcji Smooth Streaming protokołu pozyskiwania. 

Podczas tworzenia tego typu element LiveEvent należy określić **Brak** (LiveEventEncodingType.None).

> [!NOTE]
> Metoda przekazywania to najbardziej ekonomiczne rozwiązanie transmisji strumieniowej na żywo w przypadku organizowania wielu wydarzeń w długim okresie oraz poczynionych inwestycji w kodery lokalne. Zobacz szczegółowe informacje o [cenach](https://azure.microsoft.com/pricing/details/media-services/).
> 

## <a name="liveevent-types-comparison"></a>Porównanie typów element LiveEvent 

W poniższej tabeli porównano funkcje dwa typy element LiveEvent.

| Cecha | Element LiveEvent przekazywania | Element LiveEvent podstawowe |
| --- | --- | --- |
| Pojedyncza szybkość transmisji bitów w danych wejściowych jest zakodowany do wielokrotnych w chmurze |Nie |Yes |
| Maksymalna rozdzielczość, liczba warstw |4Kp30  |720p 6 warstwy 30 kl. / s |
| Protokoły danych wejściowych |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Cena |Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) i kliknij kartę "Wideo na żywo" |Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) |
| Maksymalny czas działania |Całodobowo |Całodobowo |
| Obsługa wstawiania plansz |Nie |Yes |
| Obsługa ad sygnalizowanie za pośrednictwem interfejsu API|Nie |Yes |
| Obsługa ad sygnalizowanie za pośrednictwem sieci wewnątrzpasmowej SCTE35|Yes |Yes |
| Podpisy napisy kodowane CEA 608/708 przekazywania |Yes |Yes |
| Zdolność odzyskania po krótkie wstrzymania w udziale kanału informacyjnego |Yes |Nie (element LiveEvent rozpocznie się slating ponad 6 sekund bez danych wejściowych)|
| Obsługa niejednolitego GOPs danych wejściowych |Yes |Nie — dane wejściowe zostały poprawione 2 s GOPs |
| Obsługa dane wejściowe szybkość klatek zmiennej |Yes |Nie — dane wejściowe muszą zostać usunięte, szybkości klatek.<br/>Niewielkich zmian są dopuszczalne na przykład podczas sceny wysokiej ruchu. Ale kodera nie można porzucić do 10 ramek na sekundę. |
| Auto bliskie z element LiveEvent, gdy dane wejściowe źródła danych zostaną utracone |Nie |Po 12 godzinach, jeśli nie ma żadnych LiveOutput uruchamiania |

## <a name="liveevent-states"></a>Element LiveEvent stanów 

Bieżący stan element LiveEvent. Możliwe wartości obejmują:

|Stan|Opis|
|---|---|
|**Zatrzymana**| To jest wstępny stan element LiveEvent po jego utworzeniu (chyba że wybrano autostart określić). Rozliczenia nie występuje w tym stanie. W tym stanie właściwości element LiveEvent mogą być aktualizowane, ale transmisja strumieniowa jest niedozwolona.|
|**Uruchamianie**| Element LiveEvent jest uruchamiany. Rozliczenia nie występuje w tym stanie. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa. Jeśli wystąpi błąd, element LiveEvent powróci do stanu zatrzymany.|
|**Uruchamianie**| Element LiveEvent jest może przetwarzać transmisje strumieniowe na żywo. Teraz jest to fakturowania użycia. Należy zatrzymać element LiveEvent, aby uniknąć dalszych rozliczeń.|
|**Zatrzymywanie**| Element LiveEvent jest zatrzymywany. Rozliczenia nie występuje w tym stanie przejściowym. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.|
|**Usuwanie**| Element LiveEvent jest usuwana. Rozliczenia nie występuje w tym stanie przejściowym. W tym stanie nie są dozwolone ani aktualizacje, ani transmisja strumieniowa.|

## <a name="liveoutput"></a>LiveOutput

A [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) umożliwia kontrolowanie publikowania, zapisywania i funkcji DVR okno Ustawienia transmisji strumieniowej na żywo. Element LiveEvent i LiveOutput relacji jest podobny do tradycyjnych multimediach, gdzie kanał (element LiveEvent) ma stały strumień zawartości, a program (LiveOutput) obejmuje niektóre zdarzenia czasowe na ten element LiveEvent.
Można określić liczbę godzin, aby zachować zarejestrowaną zawartość dla LiveOutput, ustawiając **ArchiveWindowLength** właściwości. **ArchiveWindowLength** jest czas trwania przedział czasu ISO 8601 długość okna archiwizacji (cyfrowego rejestratora wideo lub DVR). Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin. 

**ArchiveWindowLength** mówią, maksymalna liczba klientów można wyszukać w czasie od bieżącej pozycji na żywo. LiveOutputs mogą być uruchamiane określoną ilość czasu, ale zawartość, która wykracza poza długość okna jest stale odrzucana. Wartość tej właściwości określa również, jak długo mogą być manifesty na klienta.

Każdy LiveOutput jest skojarzony z [zasobów](https://docs.microsoft.com/rest/api/media/assets)i zapisuje dane w kontenerze w usłudze Azure storage dołączonych do konta usługi Media Services. Aby opublikować LiveOutput, należy utworzyć [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) dla skojarzonego elementu zawartości. Lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Element LiveEvent obsługuje maksymalnie trzy jednocześnie uruchomione LiveOutputs, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Na przykład z wymaganiami biznesowymi potrzebna jest do emisji przeznaczonych jest 24 x 7 liniowej Kanał informacyjny na żywo, ale chcesz utworzyć "nagrania" programy przez cały dzień, aby zaoferować klientom jako zawartość na żądanie do wyświetlania zapoznać się ze zmianami.  W tym scenariuszu należy najpierw utworzyć podstawowy LiveOutput, okno archiwum krótki równej 1 godz lub szybciej w przypadku klientów dostroić jako podstawowy transmisji strumieniowej na żywo. Czy utworzyć StreamingLocator dla tego LiveOutput i opublikujesz go w aplikacji lub witryny sieci web jako źródło danych "Live".  Gdy uruchomiona jest źródło danych, można programowo utworzyć drugi LiveOutput współbieżnych na początku pokazu (lub podać niektóre uchwytów należy wcześnie w ciągu 5 minut można przycięcia później). Ten drugi LiveOutput można zatrzymać 5 minut po zakończeniu program lub wydarzenie, a następnie można utworzyć nowego StreamingLocator, aby opublikować ten program jako elementu zawartości na żądanie w katalogu aplikacji.  Ten proces można powtarzać wiele razy dla innych granice programu lub wyróżnia się, że chcesz udostępnić jako na żądanie od razu, cały czas "Live" Kanał informacyjny z pierwszym LiveOutput w dalszym ciągu emisji liniowej źródła danych.  Ponadto możesz korzystać z zalet obsługi Filtr dynamiczny trim head i zakończeniem archiwum z LiveOutput, która wprowadzona dla "nakładają się na siebie safety" między programami i osiągnąć bardziej precyzyjne początek i koniec zawartości. Zarchiwizowaną zawartość można również przesyłać do [Przekształcanie](https://docs.microsoft.com/rest/api/media/transforms) dla kodowania lub ramki dokładne używać do wielu formatów danych wyjściowych, które ma być używany jako źródło do innych usług.

## <a name="streamingendpoint"></a>StreamingEndpoint

Po przesłaniu strumienia do kanału LiveEvent można rozpocząć zdarzenie przesyłania strumieniowego poprzez utworzenie elementu zawartości oraz obiektu LiveOutput i lokalizatora przesyłania strumieniowego. Spowoduje to archiwizację strumienia i udostępnić go użytkownikom za pośrednictwem [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints).

Po utworzeniu konta usługi Media Services domyślny punkt końcowy przesyłania strumieniowego jest dodawany do swojego konta w stanie zatrzymania. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość musi być w stanie uruchomiona.

## <a name="billing"></a>Rozliczenia

Element LiveEvent rozpoczyna rozliczeń zaraz po jego stan zmienia się na "Uruchomione". Aby zatrzymać element LiveEvent z rozliczeniami, musisz zatrzymać element LiveEvent.

> [!NOTE]
> Gdy **LiveEventEncodingType** na Twojej [element LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) ustawiono Basic, Media Services automatycznie bliskie wszelkie element LiveEvent, który jest nadal w stanie "Uruchomiona" 12 godzin po źródła danych wejściowych zostaną utracone, a nie LiveOutputs już działa. Jednakże możesz nadal jest naliczana za czas, jaki element LiveEvent jest w stanie "Uruchomiona".
>

W poniższej tabeli przedstawiono, jak Stany element LiveEvent mapują przekładają się naliczanie opłat.

| Element LiveEvent stanu | Jest to rozliczeń? |
| --- | --- |
| Uruchamianie |Nie (stan przejściowy) |
| Działanie |TAK |
| Zatrzymywanie |Nie (stan przejściowy) |
| Zatrzymano |Nie |

## <a name="next-steps"></a>Kolejne kroki

[Samouczek transmisji strumieniowej na żywo](stream-live-tutorial-with-api.md)
