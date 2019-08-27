---
title: Migrowanie z systemu Windows Azure Media Encoder do Media Encoder Standard | Microsoft Docs
description: W tym temacie omówiono sposób migrowania z Azure Media Encoder do Media Encoder Standard procesora multimediów.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: 2e9008d039f014c95d473f3197b48651bdaa5a45
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019370"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Migrowanie z systemu Windows Azure Media Encoder do Media Encoder Standard

W tym artykule omówiono procedurę migrowania ze starszego procesora nośników Azure Media Encoder systemu Windows (WAME), który jest wycofywany z 30 listopada 2019 do Media Encoder Standard procesora multimediów.

Podczas kodowania plików przy użyciu WAME klienci zazwyczaj używają nazwanego ciągu wstępnie zdefiniowanego `H264 Adaptive Bitrate MP4 Set 1080p`, takiego jak. Aby przeprowadzić migrację, należy zaktualizować kod, aby używał **Media Encoder Standard** procesora multimediów zamiast WAME oraz jednego z odpowiedników [ustawień systemowych](media-services-mes-presets-overview.md) , takich jak `H264 Multiple Bitrate 1080p`. 

## <a name="migrating-to-media-encoder-standard"></a>Migrowanie do Media Encoder Standard

Oto typowy C# przykład kodu, który używa starszego składnika. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Poniżej znajduje się zaktualizowana wersja, która używa Media Encoder Standard.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Scenariusze zaawansowane 

Jeśli utworzono własne ustawienie wstępne kodowania dla WAME przy użyciu jego schematu, istnieje [odpowiedni schemat dla Media Encoder Standard](media-services-mes-schema.md). Jeśli masz pytania dotyczące sposobu mapowania starszych ustawień do nowego kodera, skontaktuj się z nami za pośrednictwemmailto:amshelp@microsoft.com  

## <a name="known-differences"></a>Znane różnice 

Media Encoder Standard jest bardziej niezawodna, niezawodna, ma lepszą wydajność i produkuje lepsze dane wyjściowe niż starszy koder WAME. W dodatku: 

* Media Encoder Standard tworzy pliki wyjściowe z inną konwencją nazewnictwa niż WAME.
* Media Encoder Standard tworzy artefakty, takie jak pliki zawierające [metadane plików wejściowych](media-services-input-metadata-schema.md) i [metadane plików wyjściowych](media-services-output-metadata-schema.md).
* Zgodnie z opisem na [stronie cennika](https://azure.microsoft.com/pricing/details/media-services/#encoding) (szczególnie w sekcji często zadawane pytania) podczas kodowania filmów wideo przy użyciu Media Encoder Standard, opłaty są naliczane na podstawie czasu trwania plików utworzonych jako dane wyjściowe. W przypadku WAME opłaty są naliczane na podstawie rozmiarów wejściowych plików wideo i wyjściowych plików wideo.

## <a name="next-steps"></a>Następne kroki

* [Starsze składniki](legacy-components.md)
* [Strona cennika](https://azure.microsoft.com/pricing/details/media-services/#encoding)
