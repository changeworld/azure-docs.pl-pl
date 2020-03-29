---
title: Ustawienia predefiniowane zadań dla standardu kodera multimediów (MES) | Dokumenty firmy Microsoft
description: W temacie przedstawiono i omówienie ustawień predefiniowanych przykładów zdefiniowanych przez usługę dla standardu mes (Media Encoder Standard).
author: Juliako
manager: femila
editor: johndeu
services: media-services
documentationcenter: ''
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 50c52369a5a957a4dd6279cac5079e2dea023106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463407"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Przykładowe ustawienia predefiniowane dla standardu kodera multimediów (MES)

**Media Encoder Standard** definiuje zestaw wstępnie zdefiniowanych ustawień kodowania systemu, których można używać podczas tworzenia zadań kodowania. Zaleca się użycie predefiniowanego ustawienia "Adaptive Streaming", jeśli chcesz zakodować wideo do przesyłania strumieniowego za pomocą usługi Media Services. Po określeniu tego ustawienia wstępnego program Media Encoder Standard [automatycznie wygeneruje drabinę szybkości transmisji bitów](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Tworzenie niestandardowych ustawień wstępnych z przykładów
Usługi Media Services w pełni obsługuje dostosowywanie wszystkich wartości w ustawieniach predefiniowanych, aby spełnić określone potrzeby i wymagania dotyczące kodowania. Jeśli chcesz dostosować ustawienia wstępne kodowania, należy rozpocząć od jednego z poniższych ustawień systemowych, które są podane w tej sekcji jako szablon dla konfiguracji niestandardowej. Aby uzyskać wyjaśnienia, co oznacza każdy element w tych ustawieniach predefiniowanych i prawidłowe wartości dla każdego elementu, zobacz temat [schematu Standardowy koder nośnika.](media-services-mes-schema.md)  
  
> [!NOTE]
>  Podczas korzystania z predefiniowanego dla kodów 4k, należy uzyskać typ jednostki `S3` zarezerwowane. Aby uzyskać więcej informacji, zobacz [Jak skalować kodowanie](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  

#### <a name="video-rotation-default-setting-in-presets"></a>Domyślne ustawienie obrotu wideo w ustawieniach predefiniowanych:
Podczas pracy ze standardem media encoder obrót wideo jest domyślnie włączony. Jeśli film wideo został nagrany na urządzeniu przenośnym w trybie pionowym, te ustawienia wstępne będą obracać je w trybie poziomym przed kodowaniem.
 
## <a name="available-presets"></a>Dostępne ustawienia wstępne: 

 [H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) tworzy zestaw 8 plików MP4 wyrównanych do GOP, od 6000 kbps do 400 kbps i dźwięku AAC 5.1.  
  
 [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) tworzy zestaw 8 plików MP4 wyrównanych do GOP, od 6000 kb/s do 400 kb/s i stereofoniczny dźwięk AAC.  
  
 [H264 Multiple Bitrate 16x9 dla systemu iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) tworzy zestaw 8 plików MP4 wyrównanych do GOP, od 8500 kbps do 200 kbps i stereo audio AAC.  
  
 [H264 Multiple Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) tworzy zestaw 5 plików MP4 wyrównanych do GOP, od 1900 kb/s do 400 kb/s i dźwięku AAC 5.1.  
  
 [H264 Multiple Bitrate 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) tworzy zestaw 5 plików MP4 wyrównanych do GOP, od 1900 kb/s do 400 kb/s i stereofoniczny dźwięk AAC.  
  
 [H264 Multiple Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) tworzy zestaw 12 plików MP4 wyrównanych do GOP, od 20000 kb/s do 1000 kb/s i dźwięku AAC 5.1.  
  
 [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) tworzy zestaw 12 plików MP4 wyrównanych do GOP, od 20000 kb/s do 1000 kb/s i stereofoniczny dźwięk AAC.  
  
 [H264 Multiple Bitrate 4x3 for iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) tworzy zestaw 8 plików MP4 wyrównanych do GOP, od 8500 kb/s do 200 kb/s i stereofoniczny dźwięk AAC.  
  
 [H264 Multiple Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) tworzy zestaw 5 plików MP4 wyrównanych do GOP, od 1600 kbps do 400 kbps i dźwięku AAC 5.1.  
  
 [H264 Multiple Bitrate 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) tworzy zestaw 5 plików MP4 wyrównanych do GOP, od 1600 kbps do 400 kbps i stereo audio AAC.  
  
 [H264 Multiple Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) tworzy zestaw 6 plików MP4 wyrównanych do GOP, od 3400 kbps do 400 kbps i dźwięku AAC 5.1.  
  
 [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) tworzy zestaw 6 plików MP4 wyrównanych do GOP, od 3400 kb/s do 400 kb/s i stereofoniczny dźwięk AAC.  
  
 [H264 Single Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) tworzy pojedynczy plik MP4 o szybkości transmisji bitów 6750 kb/s i dźwięku AAC 5.1.  
  
 [H264 Single Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) tworzy pojedynczy plik MP4 o szybkości transmisji bitów 6750 kb/s i stereofoniczny dźwięk AAC.  
  
 [H264 Single Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) tworzy pojedynczy plik MP4 o szybkości transmisji bitów 18000 kb/s i dźwięku AAC 5.1.  
  
 [H264 Single Bitrate 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) tworzy pojedynczy plik MP4 o szybkości transmisji bitów 18000 kb/s i stereofoniczny dźwięk AAC.  
  
 [H264 Single Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) tworzy pojedynczy plik MP4 o szybkości transmisji bitów 1800 kb/s i dźwięku AAC 5.1.  
  
 [H264 Single Bitrate 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) tworzy pojedynczy plik MP4 o szybkości transmisji bitów 1800 kb/s i stereofoniczny dźwięk AAC.  
  
 [H264 Single Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) tworzy pojedynczy plik MP4 o szybkości transmisji bitów 2200 kb/s i dźwięku AAC 5.1.  
  
 [H264 Single Bitrate 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) tworzy pojedynczy plik MP4 o szybkości transmisji bitów 2200 kb/s i stereofoniczny dźwięk AAC.  
  
 [H264 Single Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) tworzy pojedynczy plik MP4 o szybkości transmisji bitów 4500 kb/s i dźwięku AAC 5.1.  
  
 [H264 Single Bitrate 720p for Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) preset produkuje pojedynczy plik MP4 z szybkością transmisji bitów 2000 kb/s i stereo AAC.  
  
 [H264 Single Bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) tworzy pojedynczy plik MP4 o szybkości transmisji bitów 4500 kb/s i stereofoniczny dźwięk AAC.  
  
 [H264 Single Bitrate High Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) tworzy pojedynczy plik MP4 o szybkości transmisji bitów 500 kb/s i stereofonicznym dźwięku AAC..  
  
 [H264 Single Bitrate Low Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) tworzy pojedynczy plik MP4 o szybkości transmisji bitów 56 kb/s i stereofoniczny dźwięk AAC.  
  
 Aby uzyskać więcej informacji dotyczących koderów usług Media Services, zobacz [Kodowanie na żądanie za pomocą usługi Azure Media Services](https://azure.microsoft.com/documentation/articles/media-services-encode-asset/).
