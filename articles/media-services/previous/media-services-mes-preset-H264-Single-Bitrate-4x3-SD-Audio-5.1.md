---
title: H264 Single Bitrate 4x3 SD Audio 5.1 | Dokumenty firmy Microsoft
description: W tym temacie przedstawiono omówienie ustawienia predefiniowanego zadania **H264 Single Bitrate 4x3 SD Audio 5.1.**
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: e55dd302-2f42-46b5-ae17-bd3c72329c03
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: b3d31116ec30415a741b6175e7a50ed0169f6073
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463552"
---
# <a name="h264-single-bitrate-4x3-sd-audio-51"></a>Pojedyncza szybkość transmisji bitów H264 4x3 SD Audio 5.1
`Media Encoder Standard`definiuje zestaw ustawień predefiniowanych kodowania, których można używać podczas tworzenia zadań kodowania. Można użyć a, `preset name` aby określić, w jakim formacie chcesz zakodować plik multimedialny. Można też utworzyć własne ustawienia predefiniowane oparte na języku JSON lub XML (przy użyciu kodowania UTF-8 lub UTF-16. Następnie należy przekazać niestandardowe ustawienia predefiniowane do kodera. Aby uzyskać listę wszystkich predefiniowanych `Media Encoder Standard` nazw obsługiwanych przez ten koder, zobacz [Presety zadań dla standardu kodera multimediów](media-services-mes-presets-overview.md).  
  
 W tym `H264 Single Bitrate 4x3 SD Audio 5.1` temacie przedstawiono ustawienia predefiniowane w formacie XML i JSON.  
  
 To ustawienie wstępne tworzy pojedynczy plik MP4 o szybkości transmisji bitów 1800 kb/s i dźwięku AAC 5.1. Aby uzyskać szczegółowe informacje na temat profilu, szybkości transmisji bitów, częstotliwości próbkowania itp. Aby uzyskać wyjaśnienia dotyczące tego, co oznacza każdy element i prawidłowych wartości dla każdego elementu, zobacz [schemat Standardowy kodera multimediów](media-services-mes-schema.md).  
  
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
          <Bitrate>1800</Bitrate>  
          <Width>640</Width>  
          <Height>480</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>1800</MaxBitrate>  
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
          "Bitrate": 1800,  
          "MaxBitrate": 1800,  
          "BufferWindow": "00:00:05",  
          "Width": 640,  
          "Height": 480,  
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
