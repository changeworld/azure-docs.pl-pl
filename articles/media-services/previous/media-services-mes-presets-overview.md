---
title: Zadań wstępne dla usługi Media Encoder Standard (MES) | Dokumentacja firmy Microsoft
description: Udostępnia tematu, a omówienie przykładu zdefiniowane przez usługę ustawienia wstępne dla Media Encoder Standard (MES).
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463407"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Przykładowe ustawienia usługi Media Encoder Standard (MES)

**Usługi Media Encoder Standard** definiuje zestaw wstępnie zdefiniowanych system kodowania ustawień wstępnych, można użyć podczas tworzenia zadań kodowania. Zaleca się używać "adaptacyjnego przesyłania strumieniowego" ustawienie wstępne, jeśli chcesz kodować wideo do przesyłania strumieniowego za pomocą usługi Media Services. Po określeniu ustawienia wstępnego, spowoduje Media Encoder Standard [automatyczne generowanie drabiny szybkości transmisji bitów](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Tworzenie niestandardowych ustawień wstępnych z przykładów
Usługa Media Services w pełni obsługuje dostosowywania wszystkie wartości w ustawieniach wstępnych w celu spełnienia specyficznych potrzeb kodowania i wymagań dotyczących usługi. Jeśli potrzebujesz dostosować ustawienia wstępne kodowania, należy rozpocząć od jednego z poniższych ustawienia systemu, które znajdują się w tej sekcji jako szablonu dla niestandardowej konfiguracji. Objaśnienia każdego elementu w tych oznacza, że ustawienia i prawidłowe wartości dla każdego elementu, zobacz [schemat usługi Media Encoder Standard](media-services-mes-schema.md) tematu.  
  
> [!NOTE]
>  Korzystając z ustawienia domyślne dla koduje 4k, należy uzyskać `S3` zastrzeżone typu jednostki. Aby uzyskać więcej informacji, zobacz [jak kodowanie skalowania](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  

#### <a name="video-rotation-default-setting-in-presets"></a>Obrót wideo domyślne ustawienie wstępne:
Podczas pracy z usługi Media Encoder Standard, obracanie wideo jest domyślnie włączona. Jeśli Twoje wideo zostało zarejestrowane na urządzeniu przenośnym w orientacji pionowej, następnie predefiniowane dyżury je do orientacji poziomej przed kodowaniem.
 
## <a name="available-presets"></a>Dostępne ustawienia: 

 [Różne szybkości transmisji bitów H264 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) tworzy zbiór 8 wyrównane GOP pliki MP4 z, od 6000 KB/s do 400 KB/s i audio AAC 5.1.  
  
 [Wiele szybkość transmisji bitów H264 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) tworzy zbiór 8 wyrównane GOP pliki MP4 z, od 6000 KB/s do 400 KB/s i stereo AAC audio.  
  
 [Wiele szybkość transmisji bitów H264 16 x 9 dla systemu iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) tworzy zbiór 8 wyrównane GOP pliki MP4 z, od 8500 KB/s do 200 KB/s i stereo AAC audio.  
  
 [Wiele szybkość transmisji bitów H264 16 x 9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) tworzy zbiór 5 wyrównane GOP pliki MP4 z, od 1900 KB/s do 400 KB/s i audio AAC 5.1.  
  
 [Wiele szybkość transmisji bitów H264 16 x 9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) tworzy zbiór 5 wyrównane GOP pliki MP4 z, od 1900 KB/s do 400 KB/s i stereo AAC audio.  
  
 [Wiele szybkość transmisji bitów H264 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) tworzy zbiór 12 wyrównane GOP pliki MP4 z, od 20000 KB/s do 1000 KB/s i audio AAC 5.1.  
  
 [Wiele szybkość transmisji bitów H264 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) tworzy zbiór 12 wyrównane GOP pliki MP4 z, od 20000 KB/s do 1000 KB/s i stereo AAC audio.  
  
 [Wiele szybkość transmisji bitów H264 4 x 3 dla systemu iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) tworzy zbiór 8 wyrównane GOP pliki MP4 z, od 8500 KB/s do 200 KB/s i stereo AAC audio.  
  
 [Wiele szybkość transmisji bitów H264 4 x 3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) tworzy zbiór 5 wyrównane GOP pliki MP4 z, od 1600 KB/s do 400 KB/s i audio AAC 5.1.  
  
 [Wiele szybkość transmisji bitów H264 4 x 3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) tworzy zbiór 5 wyrównane GOP pliki MP4 z, od 1600 KB/s do 400 KB/s i stereo AAC audio.  
  
 [Różne szybkości transmisji bitów H264 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) tworzy zbiór 6 wyrównane GOP pliki MP4 z, od 3400 KB/s do 400 KB/s i audio AAC 5.1.  
  
 [Wiele szybkość transmisji bitów H264 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) tworzy zbiór 6 wyrównane GOP pliki MP4 z, od 3400 KB/s do 400 KB/s i stereo AAC audio.  
  
 [Pojedyncza szybkość transmisji bitów H264 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) tworzy pojedynczego pliku MP4 z bitrate 6750 KB/s i audio AAC 5.1.  
  
 [Pojedynczy szybkość transmisji bitów H264 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) tworzy pojedynczego pliku MP4 z 6750 KB/s i stereo AAC audio szybkość transmisji bitów.  
  
 [Pojedynczy szybkość transmisji bitów H264 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) tworzy pojedynczego pliku MP4 z bitrate 18000 KB/s i audio AAC 5.1.  
  
 [Pojedynczy szybkość transmisji bitów H264 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) tworzy pojedynczego pliku MP4 z 18000 KB/s i stereo AAC audio szybkość transmisji bitów.  
  
 [Pojedynczy szybkość transmisji bitów H264 4 x 3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) tworzy pojedynczego pliku MP4 z bitrate 1800 KB/s i audio AAC 5.1.  
  
 [Pojedynczy szybkość transmisji bitów H264 4 x 3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) tworzy pojedynczego pliku MP4 z 1800 KB/s i stereo AAC audio szybkość transmisji bitów.  
  
 [Pojedynczy szybkość transmisji bitów H264 16 x 9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) tworzy pojedynczego pliku MP4 z bitrate 2200 KB/s i audio AAC 5.1.  
  
 [Pojedynczy szybkość transmisji bitów H264 16 x 9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) tworzy pojedynczego pliku MP4 o rozmiarze 2200 KB/s i stereo AAC audio szybkość transmisji bitów.  
  
 [Pojedyncza szybkość transmisji bitów H264 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) tworzy pojedynczego pliku MP4 z bitrate 4500 KB/s i audio AAC 5.1.  
  
 [Pojedynczy szybkość transmisji bitów H264 720p dla systemu Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) ustawienie wstępne tworzy pojedynczego pliku MP4 z bitrate 2000 KB/s i stereo adaptacyjnych kontrolek aplikacji.  
  
 [Pojedynczy szybkość transmisji bitów H264 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) tworzy pojedynczego pliku MP4 z 4500 KB/s i stereo AAC audio szybkość transmisji bitów.  
  
 [H264 pojedynczej szybkości transmisji bitów wysokiej jakości SD dla systemu Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) tworzy pojedynczego pliku MP4 z bitrate 500 KB/s i stereo AAC audio...  
  
 [H264 pojedynczej szybkości transmisji bitów niskiej jakości SD dla systemu Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) tworzy pojedynczego pliku MP4 z 56 Kb/s i stereo AAC audio szybkość transmisji bitów.  
  
 Aby uzyskać więcej informacji związanych z koderów usługi Media Services, zobacz [kodowania na żądanie przy użyciu usługi Azure Media Services](https://azure.microsoft.com/documentation/articles/media-services-encode-asset/).
