---
title: H264 Single Bitrate 4K Media Encoder Standard preset - Azure | Dokumenty firmy Microsoft
description: W tym artykule przedstawiono omówienie ustawienia wstępnego zadania Media Encoder Standard "H264 Single Bitrate 4K".
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 8e437aea-8193-49a0-9ff2-4fd391c80972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c1ba173f97353a5ffdd4a9b58f99f6f817f963ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895892"
---
# <a name="h264-single-bitrate-4k"></a>Pojedyncza szybkość transmisji bitów H264 4K
`Media Encoder Standard`definiuje zestaw ustawień predefiniowanych kodowania, których można używać podczas tworzenia zadań kodowania. Można użyć a, `preset name` aby określić, w jakim formacie chcesz zakodować plik multimedialny. Można też utworzyć własne ustawienia predefiniowane oparte na języku JSON lub XML (przy użyciu kodowania UTF-8 lub UTF-16. Następnie należy przekazać niestandardowe ustawienia predefiniowane do kodera. Aby uzyskać listę wszystkich predefiniowanych `Media Encoder Standard` nazw obsługiwanych przez ten koder, zobacz [Presety zadań dla standardu kodera multimediów](media-services-mes-presets-overview.md).  
  
 W tym `H264 Single Bitrate 4K` temacie przedstawiono ustawienia predefiniowane w formacie XML i JSON.  
  
 To ustawienie wstępne tworzy pojedynczy plik MP4 o szybkości transmisji bitów 18000 kb/s i stereofoniczny dźwięk AAC. Aby uzyskać szczegółowe informacje na temat profilu, szybkości transmisji bitów, częstotliwości próbkowania itp. Aby uzyskać wyjaśnienia, co oznacza każdy element w tych ustawieniach predefiniowanych i prawidłowe wartości dla każdego elementu, zobacz temat [schematu Standardowy koder nośnika.](media-services-mes-schema.md)  
  
> [!NOTE]
>  Powinieneś otrzymać typ jednostki zarezerwowanej premium z kodami 4K. Aby uzyskać więcej informacji, zobacz [Jak skalować kodowanie](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  
  
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
      <Channels>2</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>128</Bitrate>  
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
```
