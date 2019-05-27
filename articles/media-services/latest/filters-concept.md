---
title: Definiowanie filtrów w usłudze Azure Media Services
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
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: bfe4bbae7953479f9b5b5ce9653fb3b8d4b2d092
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002378"
---
# <a name="filters"></a>Filtry

Podczas dostarczania zawartości do klientów (zdarzeń transmisji strumieniowej na żywo lub wideo na żądanie) klienta może wymagać większej elastyczności niż opisane w pliku manifestu zasobu domyślnego. Usługa Azure Media Services umożliwia definiowanie filtrów kont i zasobów filtry dla zawartości. 

Filtry są reguły po stronie serwera, które umożliwiają klientom wykonywać następujące czynności: 

- Odtwarzanie tylko na część wideo (zamiast odtwarzanie całego). Na przykład:
  - Zmniejsz manifestu, aby pokazać klipów podrzędnych wydarzenia na żywo ("podklipów filtering"), lub
  - Przytnij początku wideo ("Przycinanie wideo").
- Dostarczaj tylko określonej wersji i/lub ścieżek określonego języka, które są obsługiwane przez urządzenia, które służy do odtwarzania zawartości ("odwzorowanie filtering"). 
- Dostosuj prezentacji okna (DVR) w celu zapewnienia ograniczona długość okna funkcji DVR w odtwarzaczu ("Dostosowywanie prezentacji okno").

Usługa Media Services oferuje [manifestów dynamicznych](filters-dynamic-manifest-overview.md) oparte na wstępnie zdefiniowane filtry. Po zdefiniowaniu filtrów, klienci mogą ich używać w adresu URL przesyłania strumieniowego. Filtry można zastosować do adaptacyjną szybkością transmisji bitów, protokołów przesyłania strumieniowego: Apple HTTP Live przesyłania strumieniowego (HLS), między innymi MPEG-DASH i Smooth Streaming.

W poniższej tabeli przedstawiono przykładowe adresy URL przy użyciu filtrów:

|Protocol|Przykład|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`<br/>Dla protokołu HLS w wersji 3, użyj: `format=m3u8-aapl-v3`.|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="define-filters"></a>Zdefiniuj filtry

Istnieją dwa typy filtrów zasobów: 

* [Konto filtry](https://docs.microsoft.com/rest/api/media/accountfilters) (globalna) — można zastosować do dowolnego zasobu w ramach konta usługi Azure Media Services, okres istnienia konta.
* [Filtry zasobów](https://docs.microsoft.com/rest/api/media/assetfilters) (local) — można stosować tylko do elementu zawartości, z którym filtr został skojarzony po utworzeniu, okres istnienia zasobu. 

[Konto filtru](https://docs.microsoft.com/rest/api/media/accountfilters) i [filtr zasobów](https://docs.microsoft.com/rest/api/media/assetfilters) typy mają te same właściwości, definiując/opisu filtru. Podczas tworzenia, z wyjątkiem **filtr zasobów**, należy określić nazwę zasobu za pomocą którego chcesz skojarzyć z filtrem.

W zależności od scenariusza możesz zdecydować, jakiego rodzaju filtru jest bardziej odpowiednie (filtr zasobu lub Filtr konta). Filtry kont są odpowiednie dla profilów urządzeń (odwzorowanie filtrowanie) gdzie filtry zasobów może służyć można przycięcia do określonego zasobu.

Możesz użyć następujących właściwości do opisania filtry. 

|Name (Nazwa)|Opis|
|---|---|
|firstQuality|Pierwszy bitrate jakości filtru.|
|presentationTimeRange|Zakres czasu prezentacji. Ta właściwość jest używana do filtrowania punktów manifestu rozpoczęcia/zakończenia, długość okna prezentacji i pozycja początkowa na żywo. <br/>Aby uzyskać więcej informacji, zobacz [PresentationTimeRange](#presentationtimerange).|
|ścieżki|Warunki wybór ścieżki. Aby uzyskać więcej informacji, zobacz [ścieżki](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

Użyj tej właściwości, za pomocą **filtry zasobów**. Nie zaleca się ustawienie właściwości z **filtrów kont**.

|Name (Nazwa)|Opis|
|---|---|
|**endTimestamp**|Ma zastosowanie do materiałów wideo na żądanie (VoD).<br/>Prezentacji transmisji strumieniowej na żywo jest dyskretnie ignorowana i stosowane po zakończeniu prezentacji i dane ze strumienia staje się wideo na żądanie.<br/>Jest to wartość typu long reprezentujący punkt końcowy bezwzględne prezentacji zaokrąglona do najbliższej następnym uruchomieniu GOP. Jednostka jest skala czasu, więc będzie endTimestamp 1800000000 dla więcej niż trzy minuty.<br/>Użyj startTimestamp i endTimestamp można przycięcia fragmentów, które będą znajdować się na liście odtwarzania (manifest).<br/>Na przykład startTimestamp = 40000000 i endTimestamp = 100000000 przy użyciu skali czasu domyślne wygeneruje listy odtwarzania, który zawiera fragmenty znajdujące 4 sekundy, a następnie 10 sekund prezentacja wideo na żądanie. Jeśli fragment pokrywającej granicy, całego fragmentu będą uwzględniane w manifeście.|
|**forceEndTimestamp**|Ma zastosowanie do transmisji strumieniowej na żywo tylko.<br/>Wskazuje, czy właściwość endTimestamp musi być obecny. W przypadku opcji true endTimestamp musi być określona, lub zostanie zwrócony kod nieprawidłowe żądanie.<br/>Dozwolone wartości: FAŁSZ, PRAWDA.|
|**liveBackoffDuration**|Ma zastosowanie do transmisji strumieniowej na żywo tylko.<br/> Ta wartość określa najnowsze na żywo pozycji klient może próbować.<br/>Używanie tej właściwości, można opóźnić pozycji odtwarzania na żywo i tworzyć bufor po stronie serwera dla graczy.<br/>W tej właściwości jest jednostka skali czasu (patrz poniżej).<br/>Maksimum na żywo wycofania czas trwania to 300 sekund (3000000000).<br/>Na przykład wartość 2000000000 oznacza, że najnowsza wersja zawartości jest 20 sekund opóźnienia od rzeczywistego na żywo krawędzi.|
|**presentationWindowDuration**|Ma zastosowanie do transmisji strumieniowej na żywo tylko.<br/>Użyj presentationWindowDuration do zastosowania przesuwającego się okna fragmentów, które mają zostać objęte listy odtwarzania.<br/>W tej właściwości jest jednostka skali czasu (patrz poniżej).<br/>Na przykład ustawić presentationWindowDuration = 1200000000, aby zastosować przesuwającego się okna dwie minuty. Nośnik w ciągu 2 minut na żywo edge zostaną uwzględnione w listy odtwarzania. Jeśli fragment pokrywającej granicy, całego fragmentu zostaną uwzględnione na liście odtwarzania. Czas trwania okna prezentacji minimalna wynosi 60 sekund.|
|**startTimestamp**|Ma zastosowanie do wideo na żądanie (VoD) lub transmisji strumieniowej na żywo.<br/>Jest to wartość typu long reprezentujący punkt początkowy bezwzględne strumienia. Wartość jest zaokrąglana do najbliższej następnym uruchomieniu GOP. Jednostka jest skala czasu, więc będzie startTimestamp 150000000 15 sekund.<br/>Użyj startTimestamp i endTimestampp można przycięcia fragmentów, które będą znajdować się na liście odtwarzania (manifest).<br/>Na przykład startTimestamp = 40000000 i endTimestamp = 100000000 przy użyciu skali czasu domyślne wygeneruje listy odtwarzania, który zawiera fragmenty znajdujące 4 sekundy, a następnie 10 sekund prezentacja wideo na żądanie. Jeśli fragment pokrywającej granicy, całego fragmentu będą uwzględniane w manifeście.|
|**Skala czasu**|Dotyczy wszystkich sygnatur czasowych i czasów trwania prezentacji zakres czasu, w jednej sekundy jako liczbę przyrostów określono.<br/>Domyślna to przyrosty 10000000 - dziesięć milionów w 1 sekundę, gdy każdy przyrost jest długa 100 nanosekund.<br/>Na przykład jeśli chcesz ustawić startTimestamp na 30 sekund, można użyć wartości 300000000 podczas korzystania z domyślnej skali czasu.|

### <a name="tracks"></a>ścieżki

Należy określić listę warunków właściwość śledzenie filtru (FilterTrackPropertyConditions) podstawie, na którym ścieżki strumienia (transmisji strumieniowej na żywo lub wideo na żądanie) powinny zostać uwzględnione w manifeście utworzony dynamicznie. Filtry są połączone przy użyciu logicznych **i** i **lub** operacji.

Warunki właściwości śledzenie filtru opisano typy ścieżek, wartości (opisanych w poniższej tabeli) i operacje (równe, NotEqual). 

|Name (Nazwa)|Opis|
|---|---|
|**Szybkość transmisji bitów**|Szybkość transmisji bitów toru używana w celu filtrowania.<br/><br/>Zalecana wartość to zakresu szybkości transmisji w bitach na sekundę. Na przykład, "0-2427000".<br/><br/>Uwaga: podczas korzystania z wartością o określonej szybkości transmisji bitów, takich jak 250000 (liczba bitów na sekundę), to podejście nie jest zalecane, ponieważ dokładne szybkości transmisji mogą się zmieniać z jednego zasobu do innego.|
|**FourCC**|Użyj wartości FourCC ścieżki do filtrowania.<br/><br/>Wartość pierwszego elementu formatu koderów-dekoderów, jak to określono w [RFC 6381](https://tools.ietf.org/html/rfc6381). Obecnie obsługiwane są następujące kodery-dekodery: <br/>Wideo:: "avc1", "hev1", "hvc1"<br/>Audio: "Mp4a" (".", "WE 3"<br/><br/>Aby określić wartości FourCC ścieżek w zasobie, Pobierz i zapoznaj się z plikiem manifestu.|
|**Język**|Użyj języka ścieżki do filtrowania.<br/><br/>Wartość tagu języka, który chcesz dołączyć, jak określono w dokumencie RFC 5646. Na przykład "en".|
|**Nazwa**|Użyj nazwy ścieżki w celu filtrowania.|
|**Typ**|Użyj typu ścieżki do filtrowania.<br/><br/>Dozwolone są następujące wartości: "wideo", "audio" lub "text".|

### <a name="example"></a>Przykład

W poniższym przykładzie zdefiniowano filtr transmisji strumieniowej na żywo: 

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

## <a name="associate-filters-with-streaming-locator"></a>Kojarzenie filtrów z lokalizatora przesyłania strumieniowego

Można określić listę [aktywów lub filtry](filters-concept.md), która będzie stosowana dla Twojego [lokalizatora przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body). [Funkcji dynamicznego pakowania](dynamic-packaging-overview.md) ma zastosowanie ta lista filtrów wraz z tymi klienta określa się w adresie URL. Ta kombinacja generuje [manifestów dynamicznych](filters-dynamic-manifest-overview.md), która jest oparta na filtry w adresie URL i filtry, możesz określić na lokalizatora przesyłania strumieniowego. Zaleca się korzystania z tej funkcji, jeśli chcesz zastosować filtry, ale nie należy udostępniać nazwy filtru w adresie URL.

Zobacz poniższe przykłady:

* [Kojarzenie filtrów z lokalizatora przesyłania strumieniowego — .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Kojarzenie filtrów z lokalizatora przesyłania strumieniowego — interfejs wiersza polecenia](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="next-steps"></a>Kolejne kroki

Następujące artykuły pokazują, jak programowo utworzyć filtry.  

- [Tworzenie filtrów za pomocą interfejsów API REST](filters-dynamic-manifest-rest-howto.md)
- [Tworzenie filtrów za pomocą programu .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Tworzenie filtrów za pomocą interfejsu wiersza polecenia](filters-dynamic-manifest-cli-howto.md)

