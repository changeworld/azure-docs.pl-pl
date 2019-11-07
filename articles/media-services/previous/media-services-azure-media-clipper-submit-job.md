---
title: Przesyłanie zadań wycinków usługi Azure Media Clipper | Microsoft Docs
description: Procedura przesyłania przycinania zadań z usługi Azure Media Clipper
services: media-services
keywords: Clip; subclip; kodowanie; nośnik
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 04d0d2bb8939c8036ec6817c58f9ac2fbb3acd72
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684984"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Przesyłanie zadań przycinania z usługi Azure Media Clipper 

Usługa Azure Media Clipper wymaga zaimplementowania metody **submitSubclipCallback** w celu obsługi przesłaniania zadania. Ta funkcja służy do implementowania wpisu HTTP w danych wyjściowych programu Clipper do usługi sieci Web. Ta usługa sieci Web jest miejscem, w którym można przesłać zadanie kodowania. Dane wyjściowe narzędzia Clipper to Media Encoder Standard ustawienia wstępne kodowania dla renderowanych zadań lub ładunku interfejsu API REST dla wywołań filtru manifestu dynamicznego. Ten model przekazywania jest konieczny, ponieważ poświadczenia konta usługi Media Services nie są bezpieczne w przeglądarce klienta.

Poniższy diagram sekwencji ilustruje przepływ pracy między klientem przeglądarki, usługą sieci Web i Azure Media Services: ![diagram sekwencji sekwencyjnej usługi Azure Media Clipper](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

Na powyższym diagramie są cztery jednostki: przeglądarka użytkownika końcowego, usługa sieci Web, punkt końcowy usługi CDN obsługujący zasoby programu Clipper oraz Azure Media Services. Gdy użytkownik końcowy przechodzi do strony sieci Web, Strona pobiera zasoby JavaScript i CSS programu Clipper z punktu końcowego hostingu CDN. Użytkownik końcowy konfiguruje zadanie tworzenia wycinków lub dynamicznego wywołania filtru manifestu z przeglądarki. Gdy użytkownik końcowy przesyła zadanie lub wywołanie filtru, przeglądarka umieszcza ładunek zadania w usłudze sieci Web, którą należy wdrożyć. Ta usługa sieci Web ostatecznie przesyła zadanie przycinania lub wywołanie filtru, aby Azure Media Services przy użyciu poświadczeń konta usługi Media Services.

Poniższy przykład kodu ilustruje przykładową metodę **submitSubclipCallback** . W tej metodzie należy zaimplementować wpis HTTP dla ustawienia wstępnego kodowania Media Encoder Standard. Jeśli wpis zakończył się pomyślnie (**wynik**), **obietnica** jest rozwiązywana, w przeciwnym razie zostanie odrzucona ze szczegółami błędu.

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
Wyjściem z przesłania zadania jest Media Encoder Standard ustawienia wstępne kodowania dla renderowanego zadania lub ładunku interfejsu API REST dla filtrów dynamicznego manifestu.

## <a name="submitting-encoding-job-to-create-video"></a>Przesyłanie zadania kodowania do tworzenia wideo
Można przesłać zadanie kodowania Media Encoder Standard, aby utworzyć klip wideo z precyzyjną ramką. Zadanie kodowania wytwarza renderowane wideo, nowy pofragmentowany plik MP4.

Kontrakt wyjściowy zadania dla renderowanego wycinka jest obiektem JSON o następujących właściwościach:

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

Aby wykonać zadanie kodowania, Prześlij zadanie kodowania Media Encoder Standard ze skojarzonym ustawieniem wstępnym. Zapoznaj się z tym artykułem, aby uzyskać szczegółowe informacje dotyczące przesyłania zadań kodowania przy użyciu [zestawu .NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) lub [interfejsu API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Szybkie tworzenie klipów wideo bez kodowania
Alternatywą dla tworzenia zadania kodowania jest użycie usługi Azure Media Clipper do tworzenia filtrów dynamicznego manifestu. Filtry nie wymagają kodowania i mogą być tworzone szybko, ponieważ nie jest tworzony nowy zasób. Kontrakt wyjściowy dla wycinka filtru jest obiektem JSON o następujących właściwościach:

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

Aby przesłać wywołanie REST w celu utworzenia dynamicznego filtru manifestu, Prześlij skojarzony ładunek filtru przy użyciu [interfejsu API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).
