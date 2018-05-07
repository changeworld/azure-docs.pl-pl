---
title: "Ładowanie zasobów do funkcji tworzenia wycinków Azure Media | Dokumentacja firmy Microsoft"
description: "Kroki dotyczące ładowania zasobów do funkcji tworzenia wycinków multimediów Azure"
services: media-services
keywords: "klip subclip; kodowanie; nośnika"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6a479218ff8bd5addf4273b23c06380859e0ea08
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2017
---
# <a name="loading-assets-into-azure-media-clipper"></a>Ładowanie zasobów do funkcji tworzenia wycinków multimediów Azure
Zasoby mogą zostać załadowane do funkcji tworzenia wycinków multimediów dla platformy Azure za pomocą dwóch metod:
1. Statycznie przekazując biblioteki zasobów
2. Dynamiczne generowanie listy zasobów za pomocą interfejsu API

## <a name="statically-load-videos-into-clipper"></a>Statycznie załadować wideo do funkcji tworzenia wycinków
Statycznie załadować wideo do funkcji tworzenia wycinków, aby umożliwić użytkownikom końcowym tworzenie klipy bez zaznaczania filmy wideo z panelu wyboru zasobów.

W takim przypadku należy przekazać statycznego zestawu zasobów do funkcji tworzenia wycinków. Każdy zasobów zawiera identyfikator zasobów/filter AMS, nazwa, opublikowanego adresu URL przesyłania strumieniowego. Jeśli to konieczne, ochrony zawartości tokenu uwierzytelniania lub tablicę miniatur adresy URL mogą być przekazywane w. Jeśli przekazano miniatur są umieszczane w interfejsie. W zastosowaniach zasobów biblioteki statyczne i w małych można przekazać w kontrakcie zasobów dla każdego zasobu w bibliotece.

> [!NOTE]
> Statycznie ładowania zasobów do funkcji tworzenia wycinków, zasoby są dodawane **bezpośrednio do osi czasu** i **okienko zasobów nie są odtwarzane**. Pierwszy zasobów zostanie dodany do osi czasu i pozostałe zasoby są ułożone po prawej stronie osi czasu).

Aby załadować biblioteki statycznej zasobów, należy użyć **załadować** można przekazać w JSON reprezentację każdego zasobu. Poniższy przykładowy kod przedstawia reprezentacja JSON dla jednego elementu zawartości.

```javascript
var assets = [
    {
      /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
      "id": "my-asset-or-dynamic-manifest-asset-filter-id",
    
      /* Required: represents the asset name as shown in the Clipper interface */
      "name": "My Asset or Dynamic Manifest Asset Filter Name",
    
      /* Required: must be one of the following values: "asset" or "filter" */
      /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
      "type": "asset",
    
      /* Required */
      "source": {
    
        /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
        "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    
        /* Optional: default value "application/vnd.ms-sstr+xml" */
        "type": "application/vnd.ms-sstr+xml",
    
        /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
        "protectionInfo": [{
            "type": "AES",
            "authenticationToken": "Bearer aes-token-placeholder"
          },
          {
            "type": "PlayReady",
            "authenticationToken": "Bearer playready-token-placeholder"
          },
          {
            "type": "Widevine",
            "authenticationToken": "Bearer widevine-token-placeholder"
          },
          {
            "type": "FairPlay",
            "certificateUrl": "//example/path/to/myfairplay.der",
            "authenticationToken": "Bearer fairplay-token-placeholder"
          }
        ]
      },
    
      /* Optional: array containing thumbnail URLs for the video. */
      /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
      "thumbnails": [
        "//example/path/thumbnail_001.jpg",
        "//example/path/thumbnail_002.jpg",
        "//example/path/thumbnail_003.jpg",
        "//example/path/thumbnail_004.jpg",
        "//example/path/thumbnail_005.jpg"
        ]
    }
];

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.ready(function () {
    subclipper.load(assets);
});

```

> [!NOTE]
> Zalecane jest wywołanie metody load() metodą ready(handler), jak pokazano w poprzednim przykładzie łańcucha. Poprzednim przykładzie gwarantuje, że widżetu jest gotowy, przed załadowaniem zasoby.

> [!NOTE]
> Do miniatury adresów URL będzie działać zgodnie z oczekiwaniami osi czasu funkcji tworzenia wycinków musi być równomiernie rozłożony między wideo (oparte na czas trwania) w kolejności chronologicznej w tablicy. Poniższy fragment predefiniowanych JSON jako odwołanie próbki służy do generowania obrazów za pomocą procesor "Media Encoder Standard":

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="dynamically-load-videos-in-clipper"></a>Załaduj dynamicznie wideo w funkcji tworzenia wycinków
Dynamiczne ładowanie wideo do funkcji tworzenia wycinków, aby umożliwić użytkownikom końcowym wybrać pliki wideo z panelu wyboru zasobów należy przyciąć przed.

Alternatywnie można załadować zasoby dynamicznie za pośrednictwem wywołania zwrotnego. W sytuacjach, gdy zasoby są generowane dynamicznie lub biblioteki jest duży należy załadować za pośrednictwem wywołania zwrotnego. Aby załadować trwałego dynamicznie, musi implementować onLoadAssets opcjonalna funkcja wywołania zwrotnego. Ta funkcja została przekazana do funkcji tworzenia wycinków podczas inicjowania. Rozwiązany zasobów należy stosować się do sam kontrakt, co statycznie załadować zasoby. Poniższy przykładowy kod przedstawia podpis metody, oczekiwane dane wejściowe i oczekiwane dane wyjściowe.

> [!NOTE]
> Gdy dynamicznie ładowanie zasobów do funkcji tworzenia wycinków, zasoby są renderowane w **panelu wyboru zasobów**.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, retuns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: implement the getAssetsFromBackend method to get the assets from the back-end using the filter parameters (search, skip, take, type).
            getAssetsFromBackend(search, skip, take, type)
                .then(function (assets) {
                    resolve({
                        total: assets.length,
                        assets: assets
                    });
                }).catch(function () {
                    reject(Error("error details"));
                });
        });
    
        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```