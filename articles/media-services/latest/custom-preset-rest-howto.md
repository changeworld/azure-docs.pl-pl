---
title: Kodowanie transformacji niestandardowej przy użyciu usługi Media Services w wersji 3 REST — Azure | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak używać usługi Azure Media Services w wersji 3 do kodowania transformacji niestandardowej przy użyciu rest.
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
ms.openlocfilehash: 30e22cb786e5dc2a667fe41ca8edf398cf0b7613
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65761792"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Jak zakodować za pomocą niestandardowego przekształcenia - REST

Podczas kodowania za pomocą usługi Azure Media Services można szybko rozpocząć pracę z jednym z zalecanych wbudowanych ustawień wstępnych, na podstawie najlepszych rozwiązań branżowych, jak pokazano w samouczku [pliki przesyłania strumieniowego.](stream-files-tutorial-with-rest.md#create-a-transform) Można również utworzyć niestandardowe ustawienia predefiniowane, aby kierować określone wymagania dotyczące scenariusza lub urządzenia.

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas tworzenia niestandardowych ustawień predefiniowanych stosuje się następujące zagadnienia:

* Wszystkie wartości wysokości i szerokości zawartości AVC muszą być wielokrotnością 4.
* W usłudze Azure Media Services w wersji 3 wszystkie kodowanie bitów są w bitach na sekundę. Różni się to od ustawień wstępnych w naszych interfejsach API v2, które używały kilobitów na sekundę jako jednostki. Na przykład jeśli szybkość transmisji bitów w wersji 2 została określona jako 128 (kilobitów/sekundę), w wersji 3 zostanie ustawiona na 128000 (bity/sekundę).

## <a name="prerequisites"></a>Wymagania wstępne 

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). <br/>Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta usługi Media Services. 
- [Skonfiguruj listonosz dla wywołań interfejsu API REST usługi Azure Media Services](media-rest-apis-with-postman.md).<br/>Upewnij się, że postępuj zgodnie z ostatnim krokiem w temacie [Pobierz token usługi Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Definiowanie ustawień niestandardowych

Poniższy przykład definiuje treść żądania nowego przekształcenia. Definiujemy zestaw wyjść, które chcemy być generowane, gdy to przekształcenie jest używany. 

W tym przykładzie najpierw dodajemy warstwę AacAudio do kodowania audio i dwie warstwy H264Video dla kodowania wideo. W warstwach wideo przypisujemy etykiety, aby mogły być używane w nazwach plików wyjściowych. Następnie chcemy, aby dane wyjściowe zawierały również miniatury. W poniższym przykładzie określamy obrazy w formacie PNG, generowane w 50% rozdzielczości wejściowego wideo i przy trzech sygnaturach czasowych — {25%, 50%, 75} długości wejściowego wideo. Na koniec określamy format plików wyjściowych - jeden dla wideo + audio, a drugi dla miniatur. Ponieważ mamy wiele H264Layers, musimy użyć makr, które produkują unikatowe nazwy na warstwę. Możemy użyć `{Label}` lub `{Bitrate}` makro, przykład pokazuje pierwszy.

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
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
            }
        ]
    }
}

```

## <a name="create-a-new-transform"></a>Tworzenie nowej transformacji  

W tym przykładzie tworzymy **Transform,** który jest oparty na niestandardowe ustawienia predefiniowane zdefiniowane wcześniej. Podczas tworzenia transformacji należy najpierw użyć [Get,](https://docs.microsoft.com/rest/api/media/transforms/get) aby sprawdzić, czy już istnieje. Jeśli transform istnieje, należy go użyć ponownie. 

W pobranej kolekcji Listonosz wybierz pozycję **Przekształcenia i zadania tworzenia**->**lub aktualizowania transformacji**.

Metoda żądania **PUT** HTTP jest podobna do:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Wybierz kartę **Treść** i zastąp obiekt kodem json [zdefiniowanym wcześniej](#define-a-custom-preset). Aby program Media Services miał zastosowanie do określonego obrazu lub dźwięku, należy przesłać zadanie w ramach tego przekształcenia.

Wybierz pozycję **Wyślij**. 

Aby program Media Services miał zastosowanie do określonego obrazu lub dźwięku, należy przesłać zadanie w ramach tego przekształcenia. Aby uzyskać pełny przykład, który pokazuje, jak przesłać zadanie w ramach transformacji, zobacz [Samouczek: Przesyłanie strumieniowe plików wideo - REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Następne kroki

Zobacz [inne operacje REST](https://docs.microsoft.com/rest/api/media/)
