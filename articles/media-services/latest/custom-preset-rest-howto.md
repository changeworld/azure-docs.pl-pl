---
title: Kodowanie niestandardowej transformacji za pomocą usługi Media Services v3 REST — Azure | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak kodować niestandardowe przekształcenia przy użyciu usługi REST za pomocą usługi Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: a9de15530981e14e664df605cb3274c9e754ef0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733032"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Jak kodowanie za pomocą niestandardowej transformacji — REST

Podczas kodowania za pomocą usługi Azure Media Services, należy można szybko rozpocząć pracę z jedną z zalecanych wbudowane ustawienia wstępne, na podstawie najlepszych w branży, jak pokazano w [strumieniowego przesyłania plików](stream-files-tutorial-with-rest.md#create-a-transform) samouczka. Możesz również tworzyć niestandardowe ustawienie wstępne pod kątem określonych wymagań scenariusza lub urządzenia.

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas tworzenia niestandardowych ustawień wstępnych należy uwzględnić następujące kwestie:

* Wszystkie wartości dla wysokość i szerokość AVC zawartości musi być wielokrotnością liczby 4.
* W usłudze Azure Media Services v3 są wszystkie kodowania szybkości transmisji w bitach na sekundę. To różni się od ustawień wstępnych przy użyciu interfejsów API w wersji 2, które używane kilobitów na sekundę jako jednostka. Na przykład jeśli szybkość transmisji bitów w wersji 2 została określona jako 128 (kilobity/sekundę), w wersji 3 go będzie miał ustawienie 128000 (bity/sekundę).

## <a name="prerequisites"></a>Wymagania wstępne 

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). <br/>Upewnij się, że do zapamiętania nazwę grupy zasobów i nazwę konta usługi Media Services. 
- [Konfigurowanie narzędzia Postman dla wywołania interfejsu API REST usługi Azure Media Services](media-rest-apis-with-postman.md).<br/>Upewnij się, że należy wykonać ostatni krok w temacie [pobrać tokenu usługi Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Definiowanie niestandardowego ustawienia wstępnego

W poniższym przykładzie zdefiniowano treść żądania elementu nowe przekształcenie. Możemy zdefiniować zestaw danych wyjściowych, które ma zostać wygenerowane podczas tej transformacji jest używany. 

W tym przykładzie najpierw dodamy warstwy AacAudio audio kodowania i dwie warstwy H264Video dla kodowania wideo. W warstwach wideo firma Microsoft przypisać etykiety, dzięki czemu mogą być używane w nazwach plików danych wyjściowych. Następnie chcemy, aby dane wyjściowe, aby obejmować miniatury. W poniższym przykładzie określamy obrazy w formacie PNG, generowane w wysokości 50% rozdzielczość wejściowy plik wideo i w trzech sygnatury czasowe — {25%, 50%, 75} długości wejściowego filmu wideo. Ponadto firma Microsoft służy do określania pliki wyjściowe — jedno dla wideo i audio, a drugi dla miniatur. Ponieważ firma Microsoft ma wielu H264Layers, musimy użyć makra, które generują unikatowych nazw na warstwę. Możemy użyć `{Label}` lub `{Bitrate}` makra w przykładzie pokazano pierwszych.

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
                            "filenamePattern": "Video-{Basename}-{Label}{Extension}",
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

## <a name="create-a-new-transform"></a>Utwórz nowe przekształcenie  

W tym przykładzie tworzymy **Przekształcanie** opartego na predefiniowane zdefiniowanego wcześniej. Podczas tworzenia transformacji, należy najpierw użyć [uzyskać](https://docs.microsoft.com/rest/api/media/transforms/get) do sprawdzenia, jeśli już istnieje. Jeśli istnieje transformacji, należy użyć ponownie. 

W kolekcji Postman, która został pobrany, wybierz **transformacje i zadania**->**tworzenia lub aktualizacji Przekształcanie**.

**Umieścić** metoda żądania HTTP jest podobny do:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Wybierz **treści** kartę i Zastąp treść kodem json kod [wcześniej zdefiniowaną](#define-a-custom-preset). Media Services zastosować przekształcenia do określonego filmu wideo lub audio musisz przesłać zadanie w ramach tej transformacji.

Wybierz pozycję **Wyślij**. 

Media Services zastosować przekształcenia do określonego filmu wideo lub audio musisz przesłać zadanie w ramach tej transformacji. Aby uzyskać kompletny przykład, który pokazuje, jak można przesłać zadania w obszarze transformacji, zobacz [samouczka: Stream pliki wideo — REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Kolejne kroki

Zobacz [inne operacje REST](https://docs.microsoft.com/rest/api/media/)
