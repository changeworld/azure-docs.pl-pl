---
title: Jak przyciąć klipy wideo za pomocą media encoder Standard — Azure | Dokumenty firmy Microsoft
description: Przycinanie to proces wybierania prostokątnego okna w ramce wideo i kodowania tylko pikseli w tym oknie. W tym artykule pokazano, jak przycinać filmy za pomocą media encoder standard.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 059816284e39c65bb772bd02f066d73da624722f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887768"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Przycinanie wideo za pomocą usługi Media Encoder Standard  

Do przycinania wejściowego wideo wideo można użyć media encoder Standard (MES). Przycinanie to proces wybierania prostokątnego okna w ramce wideo i kodowania tylko pikseli w tym oknie. Poniższy diagram pomaga zilustrować proces.

![Przycinanie klipu wideo](./media/media-services-crop-video/media-services-crop-video01.png)

Załóżmy, że na wejściu jest film o rozdzielczości 1920x1080 pikseli (proporcje 16:9), ale ma czarne paski (pola filarów) po lewej i prawej stronie, dzięki czemu tylko okno 4:3 lub 1440x1080 pikseli zawiera aktywny film wideo. Za pomocą mes do przycinania lub edytowania czarnych pasków i kodowania regionu 1440x1080.

Przycinanie w mes jest etapem przetwarzania wstępnego, więc parametry przycinania w ustawieniach kodowania mają zastosowanie do oryginalnego wideo wejściowego. Kodowanie jest kolejnym etapem, a ustawienia szerokości/wysokości mają zastosowanie do *wstępnie przetworzonego* wideo, a nie do oryginalnego wideo. Podczas projektowania ustawień wstępnych należy wykonać następujące czynności: (a) wybrać parametry kadrowania na podstawie oryginalnego wejściowego wideo oraz (b) wybrać ustawienia kodowania na podstawie przyciętego wideo. Jeśli ustawienia kodowania nie są zgodne z przyciętym filmem, dane wyjściowe nie będą zgodne z oczekiwaniami.

W [poniższym](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) temacie pokazano, jak utworzyć zadanie kodowania za pomocą usługi MES i jak określić niestandardowe ustawienia predefiniowane dla zadania kodowania. 

## <a name="creating-a-custom-preset"></a>Tworzenie ustawień niestandardowych
W przykładzie pokazanym na diagramie:

1. Oryginalne wejście to 1920x1080
2. Musi być przycięty do wyjścia 1440x1080, który jest wyśrodkowany w ramce wejściowej
3. Oznacza to przesunięcie X (1920 – 1440)/2 = 240, a przesunięcie Y o zero
4. Szerokość i wysokość prostokąta Crop to odpowiednio 1440 i 1080
5. Na etapie kodowania, poprosić jest do produkcji trzech warstw, są rozdzielczości 1440x1080, 960x720 i 480x360, odpowiednio

### <a name="json-preset"></a>Ustawienia wstępne JSON
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
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
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


## <a name="restrictions-on-cropping"></a>Ograniczenia dotyczące przycinania
Funkcja przycinania ma być ręczna. Należy załadować wejściowy film wideo do odpowiedniego narzędzia do edycji, które pozwala wybrać klatki zainteresowania, ustawić kursor, aby określić przesunięcia prostokąta przycinania, aby określić ustawienie kodowania, które jest dostrojone dla danego wideo, itp. Ta funkcja nie jest przeznaczona do włączania takich rzeczy jak: automatyczne wykrywanie i usuwanie czarnych obramowań skrzynki pocztowej / pillarbox w wejściowym filmie wideo.

Następujące ograniczenia mają zastosowanie do operacji przycinania. Jeśli nie są one spełnione, zakodowanie Task może zakończyć się niepowodzeniem lub spowodować nieoczekiwane dane wyjściowe.

1. Współrzędne i rozmiar prostokąta Kadrowanie muszą zmieścić się w wejściowym filmie wideo
2. Jak wspomniano powyżej, wysokość & szerokości w ustawieniach kodowania musi odpowiadać przyciętemu wideo
3. Przycinanie dotyczy filmów zarejestrowanych w trybie poziomym (tj. nie dotyczy filmów nagranych za pomocą smartfona trzymanego pionowo lub w trybie pionowym)
4. Najlepiej sprawdza się w przypadku progresywnego wideo przechwyconego z pikselami kwadratowymi

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Następny krok
Zobacz ścieżki szkoleniowe usługi Azure Media Services, aby dowiedzieć się więcej o wspaniałych funkcjach oferowanych przez usługę AMS.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
