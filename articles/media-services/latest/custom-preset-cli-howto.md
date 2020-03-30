---
title: Kodowanie transformacji niestandardowej przy użyciu narzędzia Media Services w wersji 3 Azure CLI | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak używać usługi Azure Media Services w wersji 3 do kodowania transformacji niestandardowej przy użyciu interfejsu wiersza polecenia platformy Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 7c1b446ccf04199449f012e738f6a03660735f50
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382957"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>Jak zakodować za pomocą niestandardowego przekształcenia — azure cli

Podczas kodowania za pomocą usługi Azure Media Services można szybko rozpocząć pracę z jednym z zalecanych wbudowanych ustawień wstępnych, na podstawie najlepszych rozwiązań branżowych, jak pokazano w szybki start [plików przesyłania strumieniowego.](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) Można również utworzyć niestandardowe ustawienia predefiniowane, aby kierować określone wymagania dotyczące scenariusza lub urządzenia.

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas tworzenia niestandardowych ustawień predefiniowanych stosuje się następujące zagadnienia:

* Wszystkie wartości wysokości i szerokości zawartości AVC muszą być wielokrotnością 4.
* W usłudze Azure Media Services w wersji 3 wszystkie kodowanie bitów są w bitach na sekundę. Różni się to od ustawień wstępnych w naszych interfejsach API v2, które używały kilobitów na sekundę jako jednostki. Na przykład jeśli szybkość transmisji bitów w wersji 2 została określona jako 128 (kilobitów/sekundę), w wersji 3 zostanie ustawiona na 128000 (bity/sekundę).

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz konto usługi Media Services](create-account-cli-how-to.md).

Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta usługi Media Services.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Definiowanie ustawień niestandardowych

Poniższy przykład definiuje treść żądania nowego przekształcenia. Definiujemy zestaw wyjść, które chcemy być generowane, gdy to przekształcenie jest używany.

W tym przykładzie najpierw dodajemy warstwę AacAudio do kodowania audio i dwie warstwy H264Video dla kodowania wideo. W warstwach wideo przypisujemy etykiety, aby mogły być używane w nazwach plików wyjściowych. Następnie chcemy, aby dane wyjściowe zawierały również miniatury. W poniższym przykładzie określamy obrazy w formacie PNG, generowane w 50% rozdzielczości wejściowego wideo i przy trzech sygnaturach czasowych — {25%, 50%, 75} długości wejściowego wideo. Na koniec określamy format plików wyjściowych - jeden dla wideo + audio, a drugi dla miniatur. Ponieważ mamy wiele H264Layers, musimy użyć makr, które produkują unikatowe nazwy na warstwę. Możemy użyć `{Label}` lub `{Bitrate}` makro, przykład pokazuje pierwszy.

Mamy zamiar zapisać tę transformację w pliku. W tym przykładzie nazwa `customPreset.json`pliku .

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
            "step": "25%",
            "range": "80%",
            "layers": [
                {
                    "width": "50%",
                    "height": "50%"
                }
            ]
        }
    ],
    "formats": [
        {
            "@odata.type": "#Microsoft.Media.Mp4Format",
            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
            "outputFiles": []
        },
        {
            "@odata.type": "#Microsoft.Media.PngFormat",
            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
        }
    ]
}
```

## <a name="create-a-new-transform"></a>Tworzenie nowej transformacji  

W tym przykładzie tworzymy **Transform,** który jest oparty na niestandardowe ustawienia predefiniowane zdefiniowane wcześniej. Podczas tworzenia transformacji należy najpierw sprawdzić, czy już istnieje. Jeśli transform istnieje, należy go użyć ponownie. Następujące `show` polecenie zwraca `customTransformName` transform, jeśli istnieje:

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

Następujące polecenie interfejsu wiersza polecenia platformy Azure tworzy transform na podstawie niestandardowego ustawienia predefiniowane (zdefiniowane wcześniej).

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Aby program Media Services miał zastosowanie do określonego obrazu lub dźwięku, należy przesłać zadanie w ramach tego przekształcenia. Aby uzyskać pełny przykład przedstawiający sposób przesyłania zadania w ramach transformacji, zobacz [Szybki start: Przesyłanie strumieniowe plików wideo — Azure CLI](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Zobacz też

[Interfejs wiersza polecenia platformy Azure](/cli/azure/ams)
