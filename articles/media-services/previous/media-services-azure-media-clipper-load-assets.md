---
title: Ładowanie zasobów do usługi Azure Media Clipper | Microsoft Docs
description: Kroki ładowania zasobów do usługi Azure Media Clipper
services: media-services
keywords: Clip; subclip; kodowanie; nośnik
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 66b4ca5b2859dd306f6eb1c669a07840189f53d5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685043"
---
# <a name="loading-assets-into-azure-media-clipper"></a>Ładowanie zasobów do usługi Azure Media Clipper  

Zasoby mogą być ładowane do usługi Azure Media Clipper przez dwie metody:
1. Statyczne przekazywanie w bibliotece zasobów
2. Dynamiczne generowanie listy zasobów za pośrednictwem interfejsu API

## <a name="statically-load-videos-into-clipper"></a>Statycznie Ładuj wideo do programu Clipper
Statycznie Ładuj wideo do programu Clipper, aby umożliwić użytkownikom końcowym tworzenie klipów bez wybierania wideo z panelu wybór zasobów.

W takim przypadku do programu Clipper zostanie przekazany statyczny zestaw zasobów. Każdy element zawartości zawiera identyfikator zasobu/filtru AMS, nazwę, opublikowany adres URL przesyłania strumieniowego. W razie potrzeby można przekazywać token uwierzytelniania lub tablicę adresów URL miniatur. W przypadku przekazania miniatury są wypełniane w interfejsie. W scenariuszach, w których Biblioteka zasobów jest statyczna i niewielka, można przekazać kontrakt zasobów dla każdego elementu zawartości w bibliotece.

> [!NOTE]
> W przypadku statycznego ładowania zasobów do programu Clipper elementy zawartości są dodawane **bezpośrednio do osi czasu** , a **okienko zasobów nie jest renderowane**. Pierwszy element zawartości jest dodawany do osi czasu, a reszta zasobów jest układana po prawej stronie osi czasu.

Aby załadować statyczną bibliotekę zasobów, użyj metody **Load** , aby przekazać reprezentację JSON każdego elementu zawartości. Poniższy przykład kodu ilustruje reprezentację JSON dla jednego zasobu.

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
> Zaleca się przetworzenie łańcucha wywołania metody Load () z gotowym (Handler) metodą, jak pokazano w poprzednim przykładzie. Poprzedni przykład gwarantuje, że widżet jest gotowy przed załadowaniem zasobów.

> [!NOTE]
> Aby adresy URL miniatur działały zgodnie z oczekiwaniami na osi czasu programu Clipper, muszą one być równomiernie rozłożone na wideo (na podstawie czasu trwania) i w kolejności chronologicznej w obrębie tablicy. Poniższego fragmentu kodu JSON można użyć jako odwołania przykładowe do generowania obrazów z procesorem "Media Encoder Standard":

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

## <a name="dynamically-load-videos-in-clipper"></a>Dynamiczne ładowanie filmów wideo w programie Clipper
Dynamicznie Ładuj wideo do programu Clipper, aby umożliwić użytkownikom końcowym Wybieranie klipów wideo z panelu wyboru zasobów do wycinania.

Alternatywnie możesz ładować zasoby dynamicznie za pośrednictwem wywołania zwrotnego. W scenariuszach, w których zasoby są generowane dynamicznie lub biblioteka jest duża, należy załadować za pośrednictwem wywołania zwrotnego. Aby dynamicznie załadować zasób, należy zaimplementować opcjonalną funkcję wywołania zwrotnego onLoadAssets. Ta funkcja jest przenoszona do programu Clipper podczas inicjalizacji. Rozwiązane zasoby powinny być zgodne z tą samą umową jak zasoby ładowane statycznie. Poniższy przykład kodu ilustruje sygnaturę metody, oczekiwano danych wejściowych i oczekiwanych danych wyjściowych.

> [!NOTE]
> W przypadku dynamicznego ładowania zasobów do programu Clipper zasoby są renderowane w **panelu wybór elementu zawartości**.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, returns an object containing an array of assets (input contract)
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
