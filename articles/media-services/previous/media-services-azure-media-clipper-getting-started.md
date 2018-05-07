---
title: "Wprowadzenie do korzystania z funkcji tworzenia wycinków Azure Media | Dokumentacja firmy Microsoft"
description: "Wprowadzenie do korzystania z usługi Azure Media funkcji tworzenia wycinków, narzędzia do tworzenia klipów wideo z zasobów AMS"
services: media-services
keywords: "klip subclip; kodowanie; nośnika"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ac64d97aeeef6147aa62658c9ee440bf058f4db1
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2017
---
# <a name="create-clips-with-azure-media-clipper"></a>Utwórz klipy z funkcji tworzenia wycinków multimediów Azure
W tej sekcji przedstawiono podstawowe kroki wprowadzenie do korzystania z funkcji tworzenia wycinków Azure Media. Sekcje zawierają szczegółowe informacje na temat konfigurowania funkcji tworzenia wycinków Azure nośnika.

- Najpierw dodaj poniższe linki do usługi Azure Media Player i funkcji tworzenia wycinków Azure Media head dokumentu. Firma Microsoft zaleca, jawnie określając wersję funkcji tworzenia wycinków i usługi Azure Media Player w adresach URL. Nie należy używać najnowszej wersji tych zasobów w środowisku produkcyjnym, ponieważ są one może ulec zmianie na żądanie.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Następnie dodaj następujące klasy, do której chcesz utworzyć wystąpienia funkcji tworzenia wycinków element div.

```javascript
<div id="root" class="azure-subclipper" />
```

Opcjonalnie Aby włączyć motywu ciemny, należy dodać klasy skórki ciemny:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Następnie można utworzyć wystąpienia funkcji tworzenia wycinków z następujące wywołanie interfejsu API:

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

Parametry dla wywołania metody inicjowania są:
- `selector`{WYMAGANE, string}: selektor CSS pasującego elementu HTML, gdzie mają być renderowane elementu widget.
- `restVersion`{WYMAGANE, string}: Azure Media Services wersja interfejsu API REST do docelowego. Wersja REST definiuje format danych wyjściowych wygenerowanych przez elementu widget. Aktualnie obsługiwana jest tylko 2.0.
- `submitSubclipCallback`{WYMAGANE, promise} Funkcja wywołania zwrotnego wywoływana, gdy kliknięto przycisk "przesłać" widżetu. Funkcja wywołania zwrotnego należy oczekiwane dane wyjściowe, generowane przez element widget (Konfiguracja zadania renderowania lub definicji filtru). Aby uzyskać więcej informacji zobacz Wywołanie zwrotne subclip przesyłania.
- `logLevel`{OPCJONALNE, {"info", "ostrzeżenie", "błąd"}}: poziom rejestrowania, który będzie wyświetlany w konsoli przeglądarki. Wartość domyślna: błąd
- `minimumMarkerGap`{OPCJONALNE, int}: minimalny rozmiar subclip (w sekundach). Uwaga: wartość powinna być większa lub równa 6, która jest również wartość domyślna.
- `singleBitrateMp4Profile`{Obiekt JSON opcjonalne} Profil mp4 pojedynczej szybkości transmisji bitów do użycia na potrzeby konfiguracji zadania renderowania generowane przez elementu widget. Jeśli nie zostanie podana, używa [pojedynczej szybkości transmisji bitów MP4 profil domyślny](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile`{Obiekt JSON opcjonalne} Profil wielokrotnej szybkości transmisji bitów mp4 do użycia na potrzeby renderowania zadania konfiguracji wygenerowane przez elementu widget. Jeśli nie zostanie podana, używa [wielokrotnej szybkości transmisji bitów MP4 profil domyślny](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap`{Obiekt json opcjonalne} Umożliwia dostosowywanie skrótów klawiaturowych widżetu. Aby uzyskać więcej informacji, zobacz [można dostosować skróty klawiaturowe](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback`{Promise opcjonalne} Funkcja wywołania zwrotnego wywoływana w celu załadowania (asynchronicznie) nową stronę zasobów na do panelu zasoby za każdym razem, gdy użytkownik przewija widok w dół do dolnej części okienka. Aby uzyskać więcej informacji zobacz wywołania zwrotnego modułu ładującego okienku zasobów.
- `height`{Numer opcjonalne} Całkowita wysokość elementu widget (wysokość minimalna wynosi 600 pikseli bez okienku zasobów i 850 pikseli z okienkiem zasobów).
- `subclippingMode`(Opcjonalne, {"all", "renderowania", "Filtruj"}): tryb subclipping dozwolone. Wartość domyślna to wszystko.
- `filterAssetsTypes`(Opcjonalne, wartość logiczna): filterAssetsTypes umożliwiają pokazywanie listy rozwijanej filtrów z okienka zasoby. Wartość domyślna to true.
- `speedLevels`(Opcjonalne, array): speedLevels pozwala na ustawienie szybkości różne poziomy dla odtwarzacza wideo, zobacz [dokumentacji usługi Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) Aby uzyskać więcej informacji.
- `resetOnJobDone`(Opcjonalne, wartość logiczna): resetOnJobDone umożliwia funkcji tworzenia wycinków zresetować subclipper do stanu początkowego, gdy zadanie jest zostało pomyślnie przesłane.
- `autoplayVideo`(Opcjonalne, wartość logiczna): autoplayVideo umożliwia funkcji tworzenia wycinków Autoodtwarzanie wideo na obciążenia. Wartość domyślna to true.
- `language`{OPCJONALNY, ciąg}: język ustawia język elementu widget. Jeśli nie określono elementu widget próbuje localize komunikaty oparte na języku przeglądarki. Jeśli języka nie zostanie wykryty w przeglądarce, widżetu domyślnie jest język angielski. Aby uzyskać więcej informacji, zobacz [skonfigurować lokalizacja](media-services-azure-media-clipper-localization.md) sekcji.
- `languages`{OPCJONALNE, JSON}: parametr języków zastępuje słownik domyślne języki słownika zdefiniowanych przez użytkownika. Aby uzyskać więcej informacji, zobacz [skonfigurować lokalizacja](media-services-azure-media-clipper-localization.md) sekcji.
- `extraLanguages`(Opcjonalne, JSON): parametr extraLanaguages dodaje nowe języki do domyślnego słownika. Aby uzyskać więcej informacji, zobacz [skonfigurować lokalizacja](media-services-azure-media-clipper-localization.md) sekcji.

## <a name="typescript-definition"></a>Definicji typeScript
A [TypeScript](https://www.typescriptlang.org/) można znaleźć pliku definicji dla funkcji tworzenia wycinków [tutaj](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>Azure Media funkcji tworzenia wycinków interfejsu API
W tej sekcji omówiono powierzchni interfejsu API dostarczonych przez funkcji tworzenia wycinków.

- `ready(handler)`: sposób do uruchamiania JavaScript, jak funkcji tworzenia wycinków jest załadowany i gotowa do użycia.
- `load(assets)`: ładuje listę zasobów do osi czasu widget (nie należy używać razem z assetsPanelLoaderCallback). Zobacz to [artykułu](media-services-azure-media-clipper-load-assets.md) szczegółowe informacje na temat sposobu ładowania zasobów do funkcji tworzenia wycinków.
- `setLogLevel(level)`: Ustawia poziom rejestrowania, który będzie wyświetlany w konsoli przeglądarki. Możliwe wartości to: `info`, `warn`, `error`.
- `setHeight(height)`: ustawia całkowity wysokość w pikselach (minimalna wysokość wynosi 600 pikseli bez okienku zasobów i 850 pikseli z okienkiem zasobów).
- `version`: pobiera wersję elementu widget.

## <a name="next-steps"></a>Następne kroki
Zobacz następne kroki konfigurowania funkcji tworzenia wycinków Azure nośnika:
- [Ładowanie zasobów do funkcji tworzenia wycinków multimediów Azure](media-services-azure-media-clipper-load-assets.md)
- [Konfigurowanie niestandardowych skrótów klawiaturowych](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Przesyłanie zadań wycinka z funkcji tworzenia wycinków](media-services-azure-media-clipper-submit-job.md)
- [Konfigurowanie lokalizacji](media-services-azure-media-clipper-localization.md)