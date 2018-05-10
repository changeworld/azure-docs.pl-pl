---
title: Jak przycinanie wideo z Media Encoder Standard - Azure | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób przycięcia wideo z Media Encoder Standard.
services: media-services
documentationcenter: ''
author: anilmur
manager: cfowler
editor: ''
ms.assetid: 7628f674-2005-4531-8b61-d7a4f53e46ba
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/09/2017
ms.author: anilmur;juliako;
ms.openlocfilehash: 2592316481c9e265fbae20b832beb21ae4905b14
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="crop-videos-with-media-encoder-standard"></a>Przycinanie wideo za pomocą usługi Media Encoder Standard
Aby przyciąć dane wejściowe wideo, można użyć Media Encoder Standard (rynkowej). Przycinanie to proces wyboru prostokątne okna w ramce wideo i kodowanie tylko pikseli w danym przedziale. Poniższy diagram ułatwia zilustrowanie tego procesu.

![Przytnij wideo](./media/media-services-crop-video/media-services-crop-video01.png)

Załóżmy, że masz jako dane wejściowe wideo ma rozdzielczość 1920 x 1080 pikseli (współczynnik proporcji 16:9), ale ma czarne paski (pola słupka) w lewo i w prawo, tak aby tylko okna 4:3 lub 1440 x 1080 pikseli zawiera aktywne wideo. Użyj rynkowej Aby przyciąć lub edytować limit czarne paski i kodowanie region 1440 x 1080 pikseli.

Przycinanie w rynkowej jest przetwarzania wstępnego etap, więc przycinania parametry w ustawienie wstępne kodowania stosuje się do oryginalnej wejściowy plik wideo. Kodowanie jest dalszym etapie i ustawienia szerokości i wysokości dotyczą *wstępnie przetworzonych* wideo, a nie do oryginalnego wideo. Podczas projektowania ustawienia należy wykonać następujące czynności: () wybierz parametrów przycięcia oparty na oryginalnym wejściowy plik wideo i (b) wybierz Twoje kodowania ustawienia oparte na przycięte wideo. Jeśli nie są zgodne z kodowania ustawienia przycięte wideo, nie będą dane wyjściowe, zgodnie z oczekiwaniami.

[Następujące](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) temacie przedstawiono sposób tworzenia zadania kodowania rynkowej oraz sposób określania niestandardowej ustawienie wstępne kodowania zadania. 

## <a name="creating-a-custom-preset"></a>Tworzenie animacji niestandardowej
W przykładzie przedstawionym w diagramie:

1. Oryginalne dane wejściowe są 1920 x 1080 pikseli
2. Należy go przycięte do wyjścia 1440 x 1080 pikseli, który skupia się w ramce wejściowych
3. Oznacza to Przesunięcie X (1920 — 1440) / 2 = 240 i Y przesunięcia o wartości zero
4. Szerokość i wysokość prostokąta przycięcia są 1440 i 1080, w odpowiednio
5. W fazie Koduj Zadaj jest przygotowanie trzy warstwy, są odpowiednio rozdzielczości 1440 x 1080 pikseli, 960 x 720 i 480 x 360

### <a name="json-preset"></a>Ustawienie wstępne JSON
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


## <a name="restrictions-on-cropping"></a>Ograniczenia dotyczące przycinanie
Oznacza, że funkcja przycinania można ręcznie. Musisz załadować wejściowy plik wideo do odpowiedniej edycji narzędzie, które pozwala wybrać ramki zainteresowań, umieść kursor ustalenie przesunięć prostokąta kadrowania, aby określić ustawienie wstępne kodowania, które dostosowana pod kątem tego konkretnego wideo, itp. Ta funkcja nie jest przeznaczona do elementów, jak włączyć: automatyczne wykrywanie i usuwanie granic czarny letterbox/pillarbox w wejściowego pliku wideo.

Następujące ograniczenia dotyczące funkcji przycinania. Te nie są spełnione, kodowanie zadań mogą zakończyć się niepowodzeniem, czy wygenerować nieoczekiwane dane wyjściowe.

1. Współrzędne i Rozmiar przycięcia musi mieścić się w wejściowy plik wideo
2. Jak wspomniano powyżej, szerokość i wysokość w ustawieniach Koduj musi odpowiadać przycięte wideo
3. Przycinanie dotyczy wideo przechwycone w trybie krajobraz (tj. nie dotyczy filmy wideo z smartphone zapisywane przechowywanych w pionie lub w trybie portret)
4. Działa najlepiej z obiektem wideo przechwycone kwadratowy pikseli

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Następny krok
Zobacz usługi Azure Media Services ścieżki szkoleniowe dotyczące Aby uzyskać więcej informacji o funkcje oferowane przez usługi AMS.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
