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
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 09de372ffdb48c00fde9a43c07f8f8b574462d1f
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2019
ms.locfileid: "56405726"
---
# <a name="define-account-filters-and-asset-filters"></a>Zdefiniuj filtry kont i zasobów filtry  

Podczas dostarczania zawartości do klientów (przesyłanie strumieniowe wydarzeń na żywo lub wideo na żądanie) klienta może wymagać większej elastyczności niż opisane w pliku manifestu zasobu domyślnego. Usługa Azure Media Services umożliwia definiowanie filtrów kont i zasobów filtry dla zawartości. 

Filtry są reguły po stronie serwera, które umożliwiają klientom wykonywać następujące czynności: 

- Odtwarzanie tylko na część wideo (zamiast odtwarzanie całego). Na przykład:
  - Zmniejsz manifestu, aby pokazać klipów podrzędnych wydarzenia na żywo ("podklipów filtering"), lub
  - Przytnij początku wideo ("Przycinanie wideo").
- Dostarczaj tylko określonej wersji i/lub ścieżek określonego języka, które są obsługiwane przez urządzenia, które służy do odtwarzania zawartości ("odwzorowanie filtering"). 
- Dostosuj prezentacji okna (DVR) w celu zapewnienia ograniczona długość okna funkcji DVR w odtwarzaczu ("Dostosowywanie prezentacji okno").

Usługa Media Services oferuje [manifestów dynamicznych](filters-dynamic-manifest-overview.md) oparte na wstępnie zdefiniowane filtry. Po zdefiniowaniu filtrów, klienci mogą ich używać w adresu URL przesyłania strumieniowego. Filtry można zastosować do adaptacyjną szybkością transmisji bitów, protokołów przesyłania strumieniowego: Apple HTTP Live przesyłania strumieniowego (HLS), między innymi MPEG-DASH i Smooth Streaming.

W poniższej tabeli przedstawiono przykładowe adresy URL przy użyciu filtrów:

|Protokół|Przykład|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

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
|presentationTimeRange|Zakres czasu prezentacji. Ta właściwość jest używana do filtrowania punktów manifestu rozpoczęcia/zakończenia, długość okna prezentacji i pozycja początkowa na żywo. <br/>Aby uzyskać więcej informacji, zobacz [PresentationTimeRange](#PresentationTimeRange).|
|ścieżki|Warunki wybór ścieżki. Aby uzyskać więcej informacji, zobacz [ścieżki](#tracks)|

### <a name="presentationtimerange"></a>PresentationTimeRange

Użyj tej właściwości, za pomocą **filtry zasobów**. Nie zaleca się ustawienie właściwości z **filtrów kont**.

|Name (Nazwa)|Opis|
|---|---|
|**endTimestamp**|Przedział czasu zakończenia bezwzględne. Ma zastosowanie do materiałów wideo na żądanie (VoD). Prezentacji na żywo jest dyskretnie ignorowana i stosowane po zakończeniu prezentacji i dane ze strumienia staje się wideo na żądanie.<br/><br/>Wartość reprezentuje punkt końcowy bezwzględne strumienia. Pobiera on zaokrąglony do najbliższej następnym uruchomieniu GOP.<br/><br/>Użyj StartTimestamp i EndTimestamp można przycięcia listy odtwarzania (manifest). Na przykład StartTimestamp = 40000000 i EndTimestamp = 100000000 wygeneruje listy odtwarzania, która zawiera multimediów między StartTimestamp i EndTimestamp. Jeśli fragment pokrywającej granicy, całego fragmentu będą uwzględniane w manifeście.<br/><br/>Zobacz też **forceEndTimestamp** definicję, która jest zgodna.|
|**forceEndTimestamp**|Dotyczy filtrów na żywo.<br/><br/>**forceEndTimestamp** jest wartością logiczną, wskazującą, czy **endTimestamp** zostało ustawione na prawidłową wartość. <br/><br/>Jeśli wartość jest **true**, **endTimestamp** wartość powinna być określona. Jeśli nie zostanie określony, zwracany jest nieprawidłowe żądanie.<br/><br/>Jeśli na przykład, aby zdefiniować filtr, który rozpoczyna się od 5 minut do wejściowego pliku wideo, a jest dostępna do końca strumienia, należy ustawić **forceEndTimestamp** o wartości false i pominąć ustawienie **endTimestamp**.|
|**liveBackoffDuration**|Dotyczy tylko na żywo. Właściwość jest używana do definiowania pozycji odtwarzania na żywo. Przy użyciu tej reguły, możesz opóźnić pozycji odtwarzania na żywo i utworzyć bufor po stronie serwera dla graczy. LiveBackoffDuration jest określana względem transmisji na żywo. Czas trwania maksymalna wycofywania na żywo to 300 sekund.|
|**presentationWindowDuration**|Ma zastosowanie do na żywo. Użyj **presentationWindowDuration** dotyczą przesuwającego się okna listy odtwarzania. Na przykład ustawić presentationWindowDuration = 1200000000, aby zastosować przesuwającego się okna dwie minuty. Nośnik w ciągu 2 minut na żywo edge zostaną uwzględnione w listy odtwarzania. Jeśli fragment pokrywającej granicy, całego fragmentu zostaną uwzględnione na liście odtwarzania. Czas trwania okna prezentacji minimalna wynosi 60 sekund.|
|**startTimestamp**|Ma zastosowanie do strumieni wideo na żądanie lub na żywo. Wartość stanowi punkt początkowy bezwzględne strumienia. Wartość jest zaokrąglana do najbliższej następnym uruchomieniu GOP.<br/><br/>Użyj **startTimestamp** i **endTimestamp** można przycięcia listy odtwarzania (manifest). Na przykład startTimestamp = 40000000 i endTimestamp = 100000000 wygeneruje listy odtwarzania, która zawiera multimediów między StartTimestamp i EndTimestamp. Jeśli fragment pokrywającej granicy, całego fragmentu będą uwzględniane w manifeście.|
|**Skala czasu**|Ma zastosowanie do strumieni wideo na żądanie lub na żywo. Skala czasu używany przez sygnatury czasowe i czasów trwania określonym powyżej. Skala czasu domyślny to 10000000. Służy alternatywne skali czasu. Wartość domyślna to 10000000 SNS (kilkuset nanosekund).|

### <a name="tracks"></a>ścieżki

Należy określić listę warunków właściwość śledzenie filtru (FilterTrackPropertyConditions) podstawie, na którym ścieżki strumienia (na żywo lub wideo na żądanie) powinny zostać uwzględnione w manifeście utworzony dynamicznie. Filtry są połączone przy użyciu logicznych **i** i **lub** operacji.

Warunki właściwości śledzenie filtru opisano typy ścieżek, wartości (opisanych w poniższej tabeli) i operacje (równe, NotEqual). 

|Name (Nazwa)|Opis|
|---|---|
|**Szybkość transmisji bitów**|Szybkość transmisji bitów toru używana w celu filtrowania.<br/><br/>Zalecana wartość to zakresu szybkości transmisji w bitach na sekundę. Na przykład, "0-2427000".<br/><br/>Uwaga: podczas korzystania z wartością o określonej szybkości transmisji bitów, takich jak 250000 (liczba bitów na sekundę), to podejście nie jest zalecane, ponieważ dokładne szybkości transmisji mogą się zmieniać z jednego zasobu do innego.|
|**FourCC**|Użyj wartości FourCC ścieżki do filtrowania.<br/><br/>Wartość pierwszego elementu formatu koderów-dekoderów, jak to określono w [RFC 6381](https://tools.ietf.org/html/rfc6381). Obecnie obsługiwane są następujące kodery-dekodery: <br/>Wideo:: "avc1", "hev1", "hvc1"<br/>Audio: "Mp4a" (".", "WE 3"<br/><br/>Można określić wartości FourCC dla ścieżek w zasobie [get i zapoznaj się z plikiem manifestu](#get-and-examine-manifest-files).|
|**Język**|Użyj języka ścieżki do filtrowania.<br/><br/>Wartość tagu języka, który chcesz dołączyć, jak określono w dokumencie RFC 5646. Na przykład "en".|
|**Nazwa**|Użyj nazwy ścieżki w celu filtrowania.|
|**Typ**|Użyj typu ścieżki do filtrowania.<br/><br/>Dozwolone są następujące wartości: "wideo", "audio" lub "text".|

## <a name="example"></a>Przykład

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

## <a name="next-steps"></a>Kolejne kroki

Następujące artykuły pokazują, jak programowo utworzyć filtry.  

- [Tworzenie filtrów za pomocą interfejsów API REST](filters-dynamic-manifest-rest-howto.md)
- [Tworzenie filtrów za pomocą programu .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Tworzenie filtrów za pomocą interfejsu wiersza polecenia](filters-dynamic-manifest-cli-howto.md)

