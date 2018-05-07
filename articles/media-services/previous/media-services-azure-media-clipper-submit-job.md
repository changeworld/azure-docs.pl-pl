---
title: "Przesyłanie zadań wycinka funkcji tworzenia wycinków Azure Media | Dokumentacja firmy Microsoft"
description: "Kroki do przesyłania zadań wycinka z funkcji tworzenia wycinków Azure Media"
services: media-services
keywords: "klip subclip; kodowanie; nośnika"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8372c405087c0dc7a000a65265bb99c395c3a8d6
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2017
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Przesyłanie zadań wycinka z funkcji tworzenia wycinków multimediów Azure
Wymaga usługi Azure Media funkcji tworzenia wycinków **submitSubclipCallback** metody, które mają zostać wdrożone dla obsługi przesyłanie zadań wycinka. Ta funkcja jest wykonywania HTTP POST funkcji tworzenia wycinków danych wyjściowych z usługą sieci web. Ta usługa sieci web jest, gdzie można przesłać zadania kodowania. Dane wyjściowe funkcji tworzenia wycinków jest albo Media Encoder Standard kodowania ustawienie wstępne renderowanych zadań lub ładunek interfejsu API REST dla wywołania dynamicznego filtru manifestu. Ten model przekazujący jest niezbędne, ponieważ poświadczenia konta usługi media services nie jest bezpieczna w przeglądarce klienta.

Na poniższym diagramie sekwencji przedstawiono przepływ pracy między klient przeglądarki, usługa sieci web, a usługi Azure Media Services: ![Diagram sekwencji funkcji tworzenia wycinków multimediów Azure](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

Na powyższym diagramie, są cztery jednostek: przeglądarki przez użytkownika końcowego, usługi sieci web, punkt końcowy CDN hosting funkcji tworzenia wycinków zasoby i usługi Azure Media Services. Gdy użytkownik przechodzi do strony sieci web, strony pobiera funkcji tworzenia wycinków JavaScript i CSS zasobów z hostingu punktu końcowego CDN. Użytkownik końcowy konfiguruje wycinka zadania lub Filtr dynamiczny manifestu wywołania z przeglądarki. Gdy użytkownik przesyła wywołanie tworzenia zadania lub filtr, przeglądarka umieszcza ładunku zadania z usługą sieci web, które należy wdrożyć. Ta usługa sieci web ostatecznie przesyła zadanie wycinka lub wywołanie tworzenia filtru usługi Azure Media Services przy użyciu multimediów usług poświadczenia konta.

Poniższy przykładowy kod przedstawia przykład **submitSubclipCallback** metody. W przypadku tej metody należy zaimplementować HTTP POST Media Encoder Standard kodowania ustawienia wstępnego. Jeśli WPIS zakończyło się pomyślnie (**wynik**), **Promise** zostanie rozwiązany, w przeciwnym razie jest odrzucane szczegóły błędu.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, retuns true if the operation was accept in the back-end; otherwise, returns false.
var onSubmitSubclip = function (subclip) {
    var promise = new Promise(function (resolve, reject) {
        // TODO: perform the back-end AJAX request to submit the subclip job.
        var result = true;

        if (/* everything turned out fine */ result) {
            resolve(result);
        }
        else {
            reject(Error("error details"));
        }
    });

    return promise;
};

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
```
Dane wyjściowe przesyłanie zadań jest ustawienie wstępne kodowania Media Encoder Standard renderowanych zadania lub ładunek interfejsu API REST dla manifest filtrów dynamicznych.

## <a name="submitting-encoding-job-to-create-video"></a>Przesyłania zadania kodowania w celu utworzenia wideo
Można przesłać Media Encoder Standard zadania kodowania w celu utworzenia dokładne ramki klipu wideo. Kodowanie produktu zadania renderowane wideo, nowy pofragmentowany plik MP4.

Kontrakt danych wyjściowych zadania dla renderowanych wycinka jest obiektem JSON z następującymi właściwościami:

```json
{
  /* Required */
  "name": "My New Subclip Output Name",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: The REST API version, currently 2.0 is supported
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id-1",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }, {
    /* Required */
    "id": "my-asset-id-2",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "job",

    /* Required if "type" === "job" */
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to sumit the subclip job.
    The encoding profile ("Codecs" property) depends on the "singleBitrateMp4Profile" and "multiBitrateMp4Profile" option parameters
    specified when creating the widget instance. */
    /* REFERENCE: https://docs.microsoft.com/azure/media-services/media-services-advanced-encoding-with-mes */
    "job": {
      "Version": 1.0,
      "Codecs": [{
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [{
            "Profile": "Auto",
            "Level": "auto",
            "Bitrate": 6750,
            "MaxBitrate": 6750,
            "BufferWindow": "00:00:05",
            "Width": 1920,
            "Height": 1080,
            "BFrames": 3,
            "ReferenceFrames": 3,
            "AdaptiveBFrame": true,
            "Type": "H264Layer",
            "FrameRate": "0/1"
          }],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [{
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
        "Format": {
          "Type": "MP4Format"
        }
      }],
      "Sources": [{
        "AssetId": "my-asset-id-1",
        "StartTime": "0.00:01:15.000",
        "Duration": "0.00:00:25.000"
      }, {
        "AssetId": "my-asset-id-2",
        "StartTime": "0.00:20:04.000",
        "Duration": "0.00:33:07.500"
      }]
    }
  }
}
```

Aby wykonać zadania kodowania, przesłać Media Encoder Standard zadania kodowania z skojarzony wstępnie. Zobacz, w tym artykule, aby uzyskać szczegółowe informacje dotyczące przesyłania kodowanie zadania przy użyciu [zestawu .NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) lub [interfejsu API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Szybkie tworzenie klipów wideo bez kodowania
Zamiast tworzenia zadania kodowania, można użyć funkcji tworzenia wycinków Azure Media do utworzenia filtrów dynamicznych manifestu. Filtry nie wymagają kodowania i można tworzyć szybko nie tworzenia nowego elementu zawartości. Kontrakt danych wyjściowych dla wycinka filtru jest obiektem JSON z następującymi właściwościami:

```json
{
  /* Required: Filter name (Alphanumeric characters and hyphens only, no whitespace) */
  "name": "FilterName",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: This subclipper version always returns '2.0' in this field.
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "filter",

    /* Required if "type" === "filter" */
    /* REFERENCE: https://docs.microsoft.com/rest/api/media/operations/assetfilter */
    "filter": {
      "PresentationTimeRange": {
        "StartTimestamp": "340000000",
        "EndTimestamp": "580000000",
        "Timescale": "10000000"
      },
      "Tracks": [{
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "video",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "audio",
          "Operator": "Equal"
        }, {
          "Property": "Name",
          "Value": "audio-track-1",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "text",
          "Operator": "Equal"
        }, {
          "Property": "Language",
          "Value": "en",
          "Operator": "Equal"
        }]
      }]
    }
  }
}
```

Aby przesłać wywołania REST do tworzenia dynamicznych filtru manifestu, przesyłanie przy użyciu ładunku skojarzonego filtru [interfejsu API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).