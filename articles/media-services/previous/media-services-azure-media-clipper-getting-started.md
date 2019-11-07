---
title: Wprowadzenie do korzystania z usługi Azure Media Clipper | Microsoft Docs
description: Wprowadzenie do korzystania z usługi Azure Media Clipper — narzędzia do tworzenia klipów wideo z zasobów AMS
services: media-services
keywords: Clip; subclip; kodowanie; nośnik
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 45ecc81967d6a95f817b10bce7f8396d9379bc94
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685079"
---
# <a name="create-clips-with-azure-media-clipper"></a>Tworzenie klipów za pomocą usługi Azure Media Clipper
W tej sekcji przedstawiono podstawowe kroki rozpoczynania pracy z usługą Azure Media Clipper. Poniższe sekcje zawierają informacje dotyczące sposobu konfigurowania usługi Azure Media Clipper.

- Najpierw Dodaj następujące linki dla Azure Media Player i usługi Azure Media Clipper do kierownika dokumentu. Zalecamy jawne określenie wersji programu Clipper i Azure Media Player w adresach URL. Nie należy używać najnowszej wersji tych zasobów w środowisku produkcyjnym, ponieważ mogą one ulec zmianie na żądanie.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Następnie Dodaj następujące klasy do elementu DIV, gdzie chcesz utworzyć wystąpienie dla programu Clipper.

```javascript
<div id="root" class="azure-subclipper" />
```

Opcjonalnie, aby włączyć motyw ciemny, Dodaj klasę ciemnego skórki:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Następnie Utwórz wystąpienie dla programu Clipper przy użyciu następującego wywołania interfejsu API:

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

Parametry wywołania metody inicjującej to:
- `selector` {REQUIRED, String}: Selektor CSS pasującego elementu HTML, w którym widżet powinien być renderowany.
- `restVersion` {REQUIRED, String}: Azure Media Services wersja interfejsu API REST dla elementu docelowego. Wersja REST definiuje format danych wyjściowych generowanych przez widżet. Obecnie obsługiwane są tylko 2,0.
- `submitSubclipCallback` {REQUIRED, Promise} funkcja wywołania zwrotnego wywoływana po kliknięciu przycisku "Prześlij" elementu widget. Funkcja wywołania zwrotnego powinna oczekiwać danych wyjściowych generowanych przez widżet (konfigurację zadania renderowania lub definicji filtru). Aby uzyskać więcej informacji, zobacz Przesyłanie podrzędnego wywołania zwrotnego.
- `logLevel` {OPTIONAL, {"info", "warn", "Error"}}: poziom rejestrowania, który ma być wyświetlany w konsoli przeglądarki. Wartość domyślna: błąd
- `minimumMarkerGap` {opcjonalne, int}: minimalny rozmiar podklipu (w sekundach). Uwaga: wartość powinna być większa lub równa 6, co jest również ustawieniem domyślnym.
- `singleBitrateMp4Profile` {OPTIONAL, obiekt JSON} profil pojedynczej szybkości transmisji bitów do użycia dla konfiguracji zadania renderowania wygenerowanej przez widżet. Jeśli nie zostanie podany, używa [domyślnego profilu MP4 o pojedynczej szybkości transmisji bitów](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile` {OPTIONAL, obiekt JSON} profil o wieloszybkości transmisji bitów do użycia dla konfiguracji zadania renderowania wygenerowanej przez widżet. Jeśli nie zostanie podany, używa [domyślnego profilu MP4 o wysokiej szybkości transmisji bitów](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap` {OPTIONAL, obiekt JSON} umożliwia dostosowywanie skrótów klawiaturowych widżetu. Aby uzyskać więcej informacji, zobacz [Dostosowywalne skróty klawiaturowe](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback` {opcjonalne, Promise} funkcja wywołania zwrotnego wywołana do załadowania (asynchronicznie) nowej strony zasobów w okienku Elementy zawartości za każdym razem, gdy użytkownik przewija w dół w dół okienka. Aby uzyskać więcej informacji, zobacz wywołanie zwrotne modułu ładującego okienka zasobów.
- `height` {OPTIONAL, Number} łączna wysokość elementu widget (minimalna wysokość to 600 pikseli bez okienka zasobów i 850 px z okienkiem zasobów).
- `subclippingMode` (opcjonalnie, {"All", "Render", "filter"}): dozwolone tryby podcinania. Wartość domyślna to ALL.
- `filterAssetsTypes` (opcjonalnie, bool): filterAssetsTypes umożliwia wyświetlanie/ukrywanie listy rozwijanej filtrów w okienku Elementy zawartości. Wartość domyślna to true.
- `speedLevels` (opcjonalnie, tablica): speedLevels umożliwia ustawianie różnych poziomów szybkości dla odtwarzacza wideo, zobacz dokumentację dotyczącą [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) , aby uzyskać więcej informacji.
- `resetOnJobDone` (opcjonalnie, bool): resetOnJobDone umożliwia Clipper Resetowanie wycinka do stanu początkowego, gdy zadanie zostanie pomyślnie przesłane.
- `autoplayVideo` (opcjonalnie, bool): autoplayVideo umożliwia programowi Clipper odtwarzanie wideo po załadowaniu. Wartość domyślna to true.
- `language` {OPTIONAL, String}: język ustawia język widżetu. Jeśli nie zostanie określony, widżet próbuje zlokalizować komunikaty na podstawie języka przeglądarki. Jeśli w przeglądarce nie wykryto żadnego języka, widżet zostanie domyślnie wyświetlony w języku angielskim. Aby uzyskać więcej informacji, zobacz sekcję [Konfigurowanie lokalizacji](media-services-azure-media-clipper-localization.md) .
- `languages` {OPTIONAL, JSON}: parametr Languages zastępuje domyślny słownik języków słownikiem niestandardowym zdefiniowanym przez użytkownika. Aby uzyskać więcej informacji, zobacz sekcję [Konfigurowanie lokalizacji](media-services-azure-media-clipper-localization.md) .
- `extraLanguages` (opcjonalnie, JSON): parametr extraLanguages dodaje nowe języki do domyślnego słownika. Aby uzyskać więcej informacji, zobacz sekcję [Konfigurowanie lokalizacji](media-services-azure-media-clipper-localization.md) .

## <a name="typescript-definition"></a>Definicja języka TypeScript
Plik definicji [TypeScript](https://www.typescriptlang.org/) dla narzędzia Clipper można znaleźć [tutaj](https://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>Interfejs API usługi Azure Media Clipper
Ta sekcja zawiera dokumenty dotyczące powierzchni interfejsu API dostarczonej przez program Clipper.

- `ready(handler)`: oferuje sposób uruchamiania języka JavaScript zaraz po całkowitym załadowaniu programu Clipper i przygotowaniu go do użycia.
- `load(assets)`: ładuje listę zasobów do osi czasu widżetu (nie należy używać razem z assetsPanelLoaderCallback). Zapoznaj się z tym [artykułem](media-services-azure-media-clipper-load-assets.md) , aby uzyskać szczegółowe informacje na temat ładowania zasobów do programu Clipper.
- `setLogLevel(level)`: ustawia poziom rejestrowania, który ma być wyświetlany w konsoli przeglądarki. Możliwe wartości to: `info`, `warn`, `error`.
- `setHeight(height)`: ustawia łączną wysokość widżetu (w pikselach) (minimalna wysokość to 600 pikseli bez okienka zasobów i 850 px z okienkiem zasoby).
- `version`: Pobiera wersję elementu widget.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z kolejnymi krokami dotyczącymi konfigurowania usługi Azure Media Clipper:
- [Ładowanie zasobów do usługi Azure Media Clipper](media-services-azure-media-clipper-load-assets.md)
- [Konfigurowanie niestandardowych skrótów klawiaturowych](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Przesyłanie zadań przycinania z programu Clipper](media-services-azure-media-clipper-submit-job.md)
- [Konfigurowanie lokalizacji](media-services-azure-media-clipper-localization.md)
