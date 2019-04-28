---
title: Wprowadzenie do usługi Azure Media Clipper | Dokumentacja firmy Microsoft
description: Wprowadzenie do usługi Azure Media Clipper, narzędzia do tworzenia klipów wideo z zasobami usługi AMS
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 51848b9ba4d18b3ac7d652cfbd97cab6b85f2ee8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466276"
---
# <a name="create-clips-with-azure-media-clipper"></a>Twórz klipy z usługi Azure Media Clipper
W tej sekcji przedstawiono podstawowe kroki przedstawiający wprowadzenie do usługi Azure Media Clipper. Sekcje zawierają szczegółowe informacje na temat konfigurowania usługi Azure Media Clipper.

- Najpierw dodaj poniższe linki dla usługi Azure Media Player i usługi Azure Media Clipper główny dokumentu. Firma Microsoft zaleca, jawnie określając wersję Clipper i usługi Azure Media Player w adresach URL. Nie należy używać najnowszej wersji tych zasobów w środowisku produkcyjnym, ponieważ są one ulec zmianie na żądanie.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Następnie dodaj następujące klasy do elementu div, gdzie chcesz utworzyć wystąpienie Clipper.

```javascript
<div id="root" class="azure-subclipper" />
```

Opcjonalnie Aby włączyć motyw ciemny, należy dodać klasę skórki ciemny:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Następnie utwórz wystąpienie Clipper przy użyciu następującego wywołania interfejsu API:

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

Parametry dla wywołania metody inicjowania są następujące:
- `selector` {WYMAGANE parametry}: Selektor CSS pasujący element HTML, gdzie ma być renderowany elementu widget.
- `restVersion` {WYMAGANE parametry}: Wersja interfejsu API REST usługi Azure Media Services do obiektu docelowego. Wersja REST definiuje format danych wyjściowych wygenerowanych przez widżet. Obecnie jest obsługiwana tylko w wersji 2.0.
- `submitSubclipCallback` {Promise wymagane} Funkcja wywołania zwrotnego wywoływana, gdy kliknięto przycisk "Prześlij" elementu widget. Funkcja wywołania zwrotnego, należy się spodziewać dane wyjściowe generowane przez widżet (konfiguracji zadania renderowania lub definicji filtru). Aby uzyskać więcej informacji zobacz przesyłanie klipu podrzędnego z wywołania zwrotnego.
- `logLevel` {OPTIONAL, {'info', 'warn', 'error'}}: Poziom rejestrowania, które mają być wyświetlane w konsoli przeglądarki. Wartość domyślna: błąd
- `minimumMarkerGap` {OPCJONALNE, int}: Minimalny rozmiar klipu podrzędnego (w sekundach). Uwaga: wartość powinna być większa lub równa 6, który jest również wartością domyślną.
- `singleBitrateMp4Profile` {Obiekt JSON opcjonalne} Pojedyncza szybkość transmisji bitów w formacie mp4 profil na potrzeby konfiguracji zadania renderowania, które są generowane przez widżet. Jeśli nie zostanie podana, używa [jedną szybkością transmisji bitów w formacie MP4 profil domyślny](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile` {Obiekt JSON opcjonalne} Profil różnej szybkości transmisji bitów mp4 na potrzeby renderowania konfiguracji zadania generowane przez widżet. Jeśli nie zostanie podana, używa [domyślny profil MP4 o różnych szybkościach transmisji bitów](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap` {Obiekt json opcjonalne} Umożliwia dostosowywanie skrótów klawiaturowych elementu widget. Aby uzyskać więcej informacji, zobacz [skróty klawiaturowe dostosowywalne](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback` {Promise opcjonalne} Funkcja wywołania zwrotnego wywoływana w celu ładowania (asynchronicznie) nową stronę zasobów okienka zasoby za każdym razem, gdy użytkownik przewija widok w dół do dolnej części okienka. Aby uzyskać więcej informacji zobacz Wywołanie zwrotne modułu ładującego w okienku zawartości.
- `height` {OPCJONALNE, number} Całkowita wysokość elementu widget (minimalna wysokość to 600 pikseli bez okienko zasobów i 850 pikseli z okienkiem zasobów).
- `subclippingMode` (OPTIONAL, {'all', 'render', 'filter'}): Dozwolone metody używać. Wartość domyślna to wszystkie.
- `filterAssetsTypes` (Opcjonalne, wartość logiczna): filterAssetsTypes umożliwiają Pokaż/Ukryj filtry listy rozwijanej w okienku zasobów. Wartość domyślna to true.
- `speedLevels` (OPCJONALNIE, tablicy): speedLevels umożliwia ustawianie poziomów inną szybkość dla odtwarzacza wideo, zobacz [dokumentacji usługi Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) Aby uzyskać więcej informacji.
- `resetOnJobDone` (Opcjonalne, wartość logiczna): resetOnJobDone umożliwia Clipper zerującego wtyczki subclipper początkowy stan, gdy zadanie zostanie przesłana pomyślnie.
- `autoplayVideo` (Opcjonalne, wartość logiczna): autoplayVideo umożliwia Clipper autoodtwarzania wideo na obciążenia. Wartość domyślna to true.
- `language` {Parametry opcjonalne,}: language określa język elementu widget. Jeśli nie zostanie określony, widżet próbuje zlokalizować komunikaty na podstawie języka przeglądarki. W przypadku wykrycia żadnego języka w przeglądarce, widżet domyślnie do języka angielskiego. Aby uzyskać więcej informacji, zobacz [Konfigurowanie lokalizacji](media-services-azure-media-clipper-localization.md) sekcji.
- `languages` {OPCJONALNE, JSON}: parametr języków zastępuje słownik domyślne języki słownika niestandardowego, zdefiniowane przez użytkownika. Aby uzyskać więcej informacji, zobacz [Konfigurowanie lokalizacji](media-services-azure-media-clipper-localization.md) sekcji.
- `extraLanguages` (OPCJONALNIE, JSON): parametr extraLanguages dodaje nowe języki do słownika domyślne. Aby uzyskać więcej informacji, zobacz [Konfigurowanie lokalizacji](media-services-azure-media-clipper-localization.md) sekcji.

## <a name="typescript-definition"></a>Definicji TypeScript
A [TypeScript](https://www.typescriptlang.org/) znajduje się plik definicji dla Clipper [tutaj](https://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>Azure Media Clipper API
W tej sekcji omówiono dostarczone przez Clipper powierzchnię interfejsu API.

- `ready(handler)`: oferuje możliwość uruchamiania kodu JavaScript, jak tylko Clipper jest w pełni załadowany i gotowe do użycia.
- `load(assets)`: ładuje listę zasobów do osi czasu widżet (nie należy używać razem z assetsPanelLoaderCallback). Zobacz ten [artykułu](media-services-azure-media-clipper-load-assets.md) Aby uzyskać szczegółowe informacje na temat sposobu ładowania zasobów Clipper.
- `setLogLevel(level)`: Ustawia poziom rejestrowania, które mają być wyświetlane w konsoli przeglądarki. Możliwe wartości to: `info`, `warn`, `error`.
- `setHeight(height)`: ustawia całkowita wysokość w pikselach (minimalna wysokość to 600 pikseli bez okienka zasobów i 850 pikseli z okienkiem zasobów).
- `version`: pobiera wersję elementu widget.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następne kroki konfigurowania usługi Azure Media Clipper:
- [Trwa ładowanie zasobów do usługi Azure Media Clipper](media-services-azure-media-clipper-load-assets.md)
- [Konfigurowanie niestandardowych skrótów klawiaturowych](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Przesyłanie zadań przycinania z Clipper](media-services-azure-media-clipper-submit-job.md)
- [Konfigurowanie lokalizacji](media-services-azure-media-clipper-localization.md)