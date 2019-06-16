---
title: Jak przycinanie wideo za pomocą usługi Media Encoder Standard — Azure | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób Przycinanie wideo za pomocą usługi Media Encoder Standard.
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
ms.author: anilmur;juliako;
ms.openlocfilehash: 9a81050fca935f688f2ff58cb04a148bf676f04b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61217222"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Przycinanie wideo za pomocą usługi Media Encoder Standard  

Media Encoder Standard (MES) umożliwia Przycinanie wideo dane wejściowe. Przycinanie to proces wyboru prostokątny okna w ramce wideo i kodowanie po prostu pikseli w tym oknie. Poniższy diagram ułatwia ilustrują proces.

![Przycinanie wideo](./media/media-services-crop-video/media-services-crop-video01.png)

Załóżmy, że masz jako dane wejściowe film wideo, który ma rozdzielczość 1920 x 1080 pikseli (proporcje 16:9), ale ma czarne paski (pola słupka) w lewo i w prawo, dzięki czemu tylko okna 4:3 lub 1440 x 1080 pikseli zawiera aktywne wideo. Przycinanie lub edytować czarne paski za pomocą usługi MES i kodowanie region 1440 x 1080 pikseli.

Przycinanie w MES jest etapie przetwarzania wstępnego, więc przycinania parametry w ustawienie wstępne kodowania stosuje się do oryginalnego wejściowego filmu wideo. Kodowanie jest kolejnym etapie i ustawienia szerokość/wysokość dotyczą *wstępnie przetworzonych* wideo, a nie oryginalnego filmu wideo. Podczas projektowania ustawienia należy wykonać następujące czynności: () wybierz parametry przycięcia oparte na oryginalnym wejściowy plik wideo i (b) wybierz swoje kodowanie ustawień na podstawie dotyczące przycięta filmu wideo. Jeśli nie są zgodne z kodowania ustawienia przycięty wideo, dane wyjściowe nie będą się zgodnie z oczekiwaniami.

[Następujące](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) temacie pokazano, jak utworzyć zadanie kodowania za pomocą usługi MES i jak określić niestandardowe ustawienie wstępne dla zadania kodowania. 

## <a name="creating-a-custom-preset"></a>Tworzenie niestandardowego ustawienia wstępnego
W przykładzie pokazano na diagramie:

1. Oryginalne dane wejściowe są 1920 x 1080 pikseli
2. Musi zostać obcięty do pliku wyjściowego o 1440 x 1080 pikseli, który skupia się w ramce danych wejściowych
3. Oznacza to, że Przesunięcie X (1920 – 1440) / 2 = 240 i Y przesunięcia o wartości zero
4. Szerokość i wysokość prostokąta przycięcia otrzymują 1440 1080, odpowiednio i
5. Na etapie Koduj Zadaj jest przygotowanie trzy warstwy, są odpowiednio rozwiązania 1440 x 1080 pikseli, 960 x 720 i 480 x 360

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


## <a name="restrictions-on-cropping"></a>Ograniczenia dotyczące przycinania
Oznacza, że funkcja przycinania można ręcznie. Będziesz potrzebować do załadowania wejściowy plik wideo do odpowiedniego narzędzia edycji, umożliwiającym zaznacz ramki zainteresowań, umieść kursor, aby określić przesunięć prostokąt przycinania, aby określić ustawienia wstępne kodowania, który jest ona dostrojona dla tego konkretnego wideo, itp. Ta funkcja nie jest przeznaczona do włączenia elementów, takich jak: automatyczne wykrywanie i usuwanie obramowań czarne letterbox/pillarbox w wejściowego pliku wideo.

Następujące ograniczenia mają zastosowanie do funkcji przycinania. Te nie są spełnione, Koduj zadanie może zakończyć się niepowodzeniem lub wygenerowanie nieoczekiwanego danych wyjściowych.

1. Współrzędne i rozmiar prostokąta przycięcia musi mieścić się w wejściowego filmu wideo
2. Jak wspomniano powyżej, szerokość i wysokość, w ustawieniach Koduj musi odpowiadać przycięty wideo
3. Przycinanie ma zastosowanie do filmów wideo jest przechwytywane w orientacji poziomej (czyli nie ma zastosowania do filmów wideo zarejestrowana przy użyciu smartphone przechowywanych w pionie lub w trybie portret)
4. Sprawdza się najlepiej progresywnego wideo jest przechwytywane kwadratowy pikseli

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Następny krok
Zobacz usługi Azure Media Services ścieżki szkoleniowe ułatwiają Dowiedz się więcej o wspaniałych funkcji oferowanych przez usługi AMS.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
