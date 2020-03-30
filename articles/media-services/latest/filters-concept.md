---
title: Definiowanie filtrów w usłudze Azure Media Services
description: W tym temacie opisano sposób tworzenia filtrów, aby klient mógł ich używać do przesyłania strumieniowego określonych sekcji strumienia. Usługa Media Services tworzy dynamiczne manifesty w celu osiągnięcia tego selektywnego przesyłania strumieniowego.
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
ms.date: 05/23/2019
ms.author: juliako
ms.openlocfilehash: fdf29924da31db0347938df89e698cb258c2336b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251468"
---
# <a name="filters"></a>Filtry

Podczas dostarczania zawartości do klientów (zdarzenia przesyłania strumieniowego na żywo lub wideo na żądanie) klient może potrzebować większej elastyczności niż opisana w pliku manifestu domyślnego zasobu. Usługa Azure Media Services oferuje [dynamiczne manifesty](filters-dynamic-manifest-overview.md) oparte na wstępnie zdefiniowanych filtrach. 

Filtry to reguły po stronie serwera, które umożliwiają klientom takie czynności, jak: 

- Odtwarzanie tylko części filmu (zamiast odtwarzania całego filmu). Przykład:
  - Zmniejsz manifest, aby wyświetlić podkomiks zdarzenia na żywo ("filtrowanie podklipów") lub
  - Przycinanie początku filmu ("przycinanie wideo").
- Dostarczaj tylko określone wersje i/lub określone ścieżki językowe obsługiwane przez urządzenie używane do odtwarzania zawartości ("filtrowanie wersji"). 
- Dostosuj okno prezentacji (DVR) w celu zapewnienia ograniczonej długości okna rejestratora w odtwarzaczu ("dostosowujące okno prezentacji").

Usługi Media Services umożliwiają tworzenie **filtrów kont** i **filtrów zasobów** dla zawartości. Ponadto wstępnie utworzone filtry można skojarzyć z **lokalizatorem przesyłania strumieniowego.**

## <a name="defining-filters"></a>Definiowanie filtrów

Istnieją dwa typy filtrów: 

* [Filtry kont](https://docs.microsoft.com/rest/api/media/accountfilters) (globalne) — mogą być stosowane do dowolnego zasobu na koncie usługi Azure Media Services, mają okres istnienia konta.
* [Filtry zasobów](https://docs.microsoft.com/rest/api/media/assetfilters) (lokalne) — mogą być stosowane tylko do zasobu, z którym filtr został skojarzony podczas tworzenia, mają okres istnienia zasobu. 

**Filtry kont** i **typy filtrów zasobów** mają dokładnie te same właściwości do definiowania/opisywania filtru. Z wyjątkiem sytuacji podczas tworzenia **filtru zasobów**należy określić nazwę zasobu, z którą ma zostać skojarzony filtr.

W zależności od scenariusza można zdecydować, jaki typ filtru jest bardziej odpowiedni (Filtr zasobów lub Filtr konta). Filtry kont są odpowiednie dla profili urządzeń (filtrowanie wersji), w których można użyć filtrów zasobów do przycinania określonego zasobu.

Do opisania filtrów należy użyć następujących właściwości. 

|Nazwa|Opis|
|---|---|
|firstQuality (Jakość)|Pierwsza jakość bitrate filtra.|
|prezentacjaTimeRange|Zakres czasu prezentacji. Ta właściwość służy do filtrowania punktów początkowych/końcowych manifestu, długości okna prezentacji i pozycji początkowej na żywo. <br/>Aby uzyskać więcej informacji, zobacz [PresentationTimeRange](#presentationtimerange).|
|Utworów|Śledzi warunki wyboru. Aby uzyskać więcej informacji, zobacz [ścieżki](#tracks)|

### <a name="presentationtimerange"></a>prezentacjaTimeRange

Użyj tej właściwości z **filtrami zasobów**. Nie zaleca się ustawiania właściwości za pomocą **filtrów kont**.

|Nazwa|Opis|
|---|---|
|**endTimestamp (sygnatura czas-zakończenia)**|Dotyczy wideo na żądanie (VoD).<br/>W przypadku prezentacji przesyłania strumieniowego na żywo jest ona po cichu ignorowana i stosowana po zakończeniu prezentacji, a strumień staje się VoD.<br/>Jest to wartość długa, która reprezentuje bezwzględny punkt końcowy prezentacji, zaokrąglona do najbliższego następnego uruchomienia GOP. Jednostka jest skala czasu, więc endTimestamp 1800000000 będzie przez 3 minuty.<br/>Użyj startTimestamp i endTimestamp, aby przyciąć fragmenty, które będą w liście odtwarzania (manifest).<br/>Na przykład startTimestamp=40000000 i endTimestamp=100000000 przy użyciu domyślnej skali czasu wygeneruje listę odtwarzania zawierającą fragmenty od 4 sekund do 10 sekund prezentacji VoD. Jeśli fragment przecina granicę, cały fragment zostanie uwzględniony w manifeście.|
|**forceEndTimestamp (forceEndTimestamp)**|Dotyczy tylko transmisji na żywo.<br/>Wskazuje, czy właściwość endTimestamp musi być obecna. Jeśli true, endTimestamp musi być określony lub zły kod żądania jest zwracany.<br/>Dozwolone wartości: fałszywe, prawdziwe.|
|**liveBackoffDuration**|Dotyczy tylko transmisji na żywo.<br/> Ta wartość definiuje najnowszą pozycję na żywo, do której klient może dążyć.<br/>Za pomocą tej właściwości można opóźnić pozycję odtwarzania na żywo i utworzyć bufor po stronie serwera dla graczy.<br/>Jednostka dla tej właściwości jest skala czasu (patrz poniżej).<br/>Maksymalny czas trwania na żywo jest 300 sekund (30000000000).<br/>Na przykład wartość 20000000000 oznacza, że najnowsza dostępna zawartość jest opóźniona o 20 sekund od rzeczywistej krawędzi na żywo.|
|**prezentacjaWindowDuration**|Dotyczy tylko transmisji na żywo.<br/>Użyj presentationWindowDuration, aby zastosować przesuwane okno fragmentów do uwzględnienia w liście odtwarzania.<br/>Jednostka dla tej właściwości jest skala czasu (patrz poniżej).<br/>Na przykład ustaw prezentacjęWindowDuration=12000000000, aby zastosować dwuminutowe okno przesuwne. Multimedia w ciągu 2 minut od krawędzi na żywo zostaną uwzględnione w liście odtwarzania. Jeśli fragment przecina granicę, cały fragment zostanie uwzględniony w liście odtwarzania. Minimalny czas trwania okna prezentacji wynosi 60 sekund.|
|**startTimestamp**|Dotyczy wideo na żądanie (VoD) lub transmisji strumieniowej na żywo.<br/>Jest to wartość długa, która reprezentuje bezwzględny punkt początkowy strumienia. Wartość zostanie zaokrąglona do najbliższego następnego uruchomienia GOP. Jednostka jest skala czasu, więc startTimestamp 1500000000 będzie na 15 sekund.<br/>Użyj startTimestamp i endTimestampp, aby przyciąć fragmenty, które będą znajdować się na liście odtwarzania (manifest).<br/>Na przykład startTimestamp=40000000 i endTimestamp=100000000 przy użyciu domyślnej skali czasu wygeneruje listę odtwarzania zawierającą fragmenty od 4 sekund do 10 sekund prezentacji VoD. Jeśli fragment przecina granicę, cały fragment zostanie uwzględniony w manifeście.|
|**skala czasu**|Dotyczy wszystkich sygnatur czasowych i czasów trwania w zakresie czasu prezentacji, określonym jako liczba przyrostów w ciągu jednej sekundy.<br/>Wartość domyślna to 10000000 - dziesięć milionów przyrostów w ciągu jednej sekundy, gdzie każdy przyrost będzie miał 100 nanosekund długości.<br/>Na przykład jeśli chcesz ustawić startTimestamp na 30 sekund, należy użyć wartości 300000000 podczas korzystania z domyślnej skali czasu.|

### <a name="tracks"></a>Utworów

Należy określić listę warunków właściwości śledzenia filtru (FilterTrackPropertyConditions) na podstawie której ścieżki strumienia (przesyłanie strumieniowe na żywo lub wideo na żądanie) powinny być uwzględnione w dynamicznie utworzonym manifeście. Filtry są łączone za pomocą logicznej operacji **AND** i **OR.**

Warunki właściwości śledzenia filtru opisują typy ścieżek, wartości (opisane w poniższej tabeli) i operacje (Równe, NotEqual). 

|Nazwa|Opis|
|---|---|
|**Bitrate**|Do filtrowania użyj szybkości transmisji bitów ścieżki.<br/><br/>Zalecaną wartością jest zakres szybkości transmisji bitów w bitach na sekundę. Na przykład "0-2427000".<br/><br/>Uwaga: chociaż można użyć określonej wartości szybkości transmisji bitów, takiej jak 250000 (bity na sekundę), takie podejście nie jest zalecane, ponieważ dokładne szybkości transmisji bitów mogą się zmieniać w poszczególnych zasobach.|
|**Fourcc**|Użyj wartości FourCC ścieżki do filtrowania.<br/><br/>Wartość jest pierwszym elementem formatu kodeków, jak określono w [RFC 6381](https://tools.ietf.org/html/rfc6381). Obecnie obsługiwane są następujące kodeki: <br/>Dla wideo: "avc1", "hev1", "hvc1"<br/>Dla audio: "mp4a", "ec-3"<br/><br/>Aby określić wartości FourCC dla ścieżek w zasobie, pobierz i sprawdź plik manifestu.|
|**Język**|Do filtrowania należy używać języka ścieżki.<br/><br/>Wartość jest tagiem języka, który chcesz dołączyć, jak określono w RFC 5646. Na przykład "en".|
|**Nazwa**|Użyj nazwy ścieżki do filtrowania.|
|**Typ**|Użyj typu ścieżki do filtrowania.<br/><br/>Dozwolone są następujące wartości: "video", "audio" lub "text".|

### <a name="example"></a>Przykład

Poniższy przykład definiuje filtr przesyłania strumieniowego na żywo: 

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="associating-filters-with-streaming-locator"></a>Kojarzenie filtrów za pomocą lokalizatora przesyłania strumieniowego

W lokalizatorze [przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body)można określić listę [filtrów zasobów lub kont.](filters-concept.md) [Pakiet pakiet dynamiczny](dynamic-packaging-overview.md) stosuje tę listę filtrów wraz z tymi, które klient określa w adresie URL. Ta kombinacja generuje [manifest dynamiczny](filters-dynamic-manifest-overview.md), który jest oparty na filtrach w adresie URL + filtry określone w lokalizatorze przesyłania strumieniowego. 

Zobacz poniższe przykłady:

* [Skojarz filtry z lokalizatorem przesyłania strumieniowego — .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Kojarzenie filtrów z lokalizatorem przesyłania strumieniowego — cli](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Aktualizowanie filtrów
 
**Lokalizatory przesyłania strumieniowego** nie można aktualizować, podczas gdy można aktualizować filtry. 

Nie zaleca się aktualizowania definicji filtrów skojarzonych z aktywnie publikowanym **lokalizatorem przesyłania strumieniowego,** zwłaszcza gdy sieć CDN jest włączona. Serwery przesyłania strumieniowego i sieci CDN mogą mieć wewnętrzne pamięci podręczne, które mogą spowodować zwracanie starych danych w pamięci podręcznej. 

Jeśli definicja filtru wymaga zmiany, należy rozważyć utworzenie nowego filtru i dodanie go do adresu URL **lokalizatora przesyłania strumieniowego** lub opublikowanie nowego **lokalizatora przesyłania strumieniowego,** który odwołuje się bezpośrednio do filtru.

## <a name="next-steps"></a>Następne kroki

W poniższych artykułach pokazano, jak programowo tworzyć filtry.  

- [Tworzenie filtrów za pomocą interfejsów API REST](filters-dynamic-manifest-rest-howto.md)
- [Tworzenie filtrów za pomocą programu .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Tworzenie filtrów za pomocą interfejsu wiersza polecenia](filters-dynamic-manifest-cli-howto.md)

