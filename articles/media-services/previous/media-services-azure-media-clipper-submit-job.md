---
title: Przesyłanie zadań przycinania usługi Azure Media Clipper | Dokumentacja firmy Microsoft
description: Kroki dotyczące przesyłanie zadań przycinania z usługi Azure Media Clipper
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: f0dc6879ccbb22dbebd57de98e4610cd593318db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61242862"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Przesyłanie zadań przycinania z usługi Azure Media Clipper 

Wymaga usługi Azure Media Clipper **submitSubclipCallback** metodę, aby zaimplementować obsługę przesyłanie zadań przycinania. Ta funkcja jest implementacji metody POST protokołu HTTP Clipper danych wyjściowych do usługi sieci web. Ta usługa sieci web jest, którego można przesłać zadania kodowania. Dane wyjściowe Clipper jest albo usługi Media Encoder Standard kodowania renderowanych zadań wstępne ustawienie lub ładunek filtr manifestów dynamicznych wywołań interfejsu API REST. Ten model przekazywania jest konieczne, ponieważ poświadczenia konta usługi media services nie są bezpieczne w przeglądarce klienta.

Na poniższym diagramie sekwencji przedstawiono przepływ pracy między klient przeglądarki, usługa sieci web i usługi Azure Media Services: ![Diagram sekwencji usługi Azure Media Clipper](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

Na powyższym diagramie są cztery jednostki: przeglądarki przez użytkownika końcowego, usługi sieci web, punkt końcowy usługi CDN, które hostują Clipper zasoby i usługi Azure Media Services. Gdy użytkownik przechodzi do strony sieci web, strona pobiera Clipper JavaScript i CSS zasoby z hostingu punktu końcowego usługi CDN. Użytkownik końcowy konfiguruje wycinka zadania lub wywołania tworzenia filtr manifestów dynamicznych z przeglądarki. Gdy użytkownik przesyła wywołania tworzenia zadań lub filtr, przeglądarka umieszcza ładunek zadania do usługi sieci web, które należy wdrożyć. Ta usługa sieci web ostatecznie przesyła zadanie wycinka lub wywołania tworzenia filtru usługi Azure Media Services za pomocą usługi media services — poświadczenia konta.

Poniższy przykład kodu ilustruje przykład **submitSubclipCallback** metody. Ta metoda służy do implementowania HTTP POST z usługi Media Encoder Standard ustawienie wstępne kodowania. Jeśli WPIS zakończyło się pomyślnie (**wynik**), **Promise** zostanie rozwiązany, w przeciwnym razie zostanie ono odrzucone z szczegóły błędu.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, returns true if the operation was accept in the back-end; otherwise, returns false.
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
Dane wyjściowe przesłania zadania jest ustawienie wstępne kodowania usługi Media Encoder Standard renderowanych zadania lub interfejsu API REST ładunek filtry manifestów dynamicznych.

## <a name="submitting-encoding-job-to-create-video"></a>Przesyłanie zadania kodowania w celu utworzenia wideo
Możesz przesłać Media Encoder Standard zadania kodowania, aby utworzyć ramkę dokładne klipu wideo. Kodowanie produktu zadania renderowania filmów wideo, nowy pofragmentowany plik MP4.

Kontrakt danych wyjściowych zadania dla renderowanych wycinka jest obiekt JSON z następującymi właściwościami:

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
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to submit the subclip job.
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

Aby wykonać zadania kodowania, przesłać Media Encoder Standard zadania kodowania za pomocą skojarzonego wstępnie. Zobacz, w tym artykule, aby uzyskać szczegółowe informacje dotyczące przesyłania kodowania zadania, wykorzystując [zestawu .NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) lub [interfejsu API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Szybkie tworzenie klipów wideo bez kodowania
Alternatywą dla tworzenia zadania kodowania, można użyć usługi Azure Media Clipper utworzyć filtry manifestów dynamicznych. Filtry nie wymaga kodowania i można go szybko utworzyć jako nie jest tworzony nowy zasób. Kontrakt danych wyjściowych dla wycinka filtru jest obiekt JSON z następującymi właściwościami:

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

Aby przesłać żądanie wywołania REST, aby utworzyć filtr manifestów dynamicznych, przesyłać za pośrednictwem ładunku skojarzony filtr [interfejsu API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).
