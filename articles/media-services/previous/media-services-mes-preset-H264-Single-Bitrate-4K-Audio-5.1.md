---
title: H264 Pojedyncza szybkość transmisji bitów 4K Audio 5.1 | Dokumentacja firmy Microsoft
description: Temat zawiera omówienie **pojedynczego szybkość transmisji bitów H264 4K Audio 5.1** ustawienie wstępne zadania.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 72cb95ac-2cd6-4ef4-9938-8f22cea04920
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 9c4b133ae74438def30f4fcd9e49a5ca5e6f9cd4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463569"
---
# <a name="h264-single-bitrate-4k-audio-51"></a>Pojedyncza szybkość transmisji bitów H264 4K Audio 5.1
`Media Encoder Standard` definiuje zestaw kodowania ustawienia wstępne, których można użyć podczas tworzenia zadań kodowania. Można użyć `preset name` do określenia do formatu, który chcesz zakodować pliku multimedialnego. Lub można utworzyć własne JSON lub XML na podstawie ustawień wstępnych (przy użyciu kodowania UTF-8 lub UTF-16. Następnie należy wprowadzić niestandardowego ustawienia wstępnego z koderem. Aby uzyskać listę wszystkich wstępnie zdefiniowanych nazw obsługiwanych przez to `Media Encoder Standard` encoder, zobacz [ustawienia wstępne zadań usługi Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 W tym temacie przedstawiono `H264 Single Bitrate 4K Audio 5.1` ustawienie wstępne (w formacie XML i JSON).  
  
 To ustawienie wstępne tworzy pojedynczego pliku MP4 z bitrate 18000 KB/s i audio AAC 5.1. Aby uzyskać szczegółowe informacje o profilu szybkości transmisji bitów, próbkowanie wskaźnik, itp., to ustawień, sprawdź zdefiniowane poniżej w formacie JSON lub XML. Objaśnienia dotyczące oznacza jaki każdy element i prawidłowe wartości dla każdego elementu, zobacz [schemat usługi Media Encoder Standard](media-services-mes-schema.md).  
  
> [!NOTE]
>  Należy pobrać jednostki zarezerwowanej Premium koduje typu z 4K. Aby uzyskać więcej informacji, zobacz [jak kodowanie skalowania](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>18000</Bitrate>  
          <Width>3840</Width>  
          <Height>2160</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>18000</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>6</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>384</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 18000,  
          "MaxBitrate": 18000,  
          "BufferWindow": "00:00:05",  
          "Width": 3840,  
          "Height": 2160,  
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
      "Channels": 6,  
      "SamplingRate": 48000,  
      "Bitrate": 384,  
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
```
