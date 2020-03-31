---
title: Migracja z usługi Azure Media Encoder do media encoder Standard | Dokumenty firmy Microsoft
description: W tym temacie omówiono sposób migracji z usługi Azure Media Encoder do procesora multimediów Media Encoder Standard.
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
ms.openlocfilehash: f8fe1b13db6473e80f0d7cdc638b775a0c8062c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513505"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Migrowanie z usługi Azure Media Encoder do media encoder Standard

W tym artykule omówiono kroki migracji z starszego procesora multimediów usługi Azure Media Encoder (AME) (który jest wycofywany) do procesora nośników Media Encoder Standard. Aby uzyskać daty wycofania, zobacz ten temat [starszych składników.](legacy-components.md)

Podczas kodowania plików za pomocą usługi AME klienci zazwyczaj `H264 Adaptive Bitrate MP4 Set 1080p`używali nazwanego ciągu predefiniowanych, takiego jak . Aby przeprowadzić migrację, kod musi zostać zaktualizowany w celu użycia procesora multimediów **Media Encoder Standard** zamiast AME i jednego z [równoważnych ustawień systemowych, takich](media-services-mes-presets-overview.md) jak `H264 Multiple Bitrate 1080p`. 

## <a name="migrating-to-media-encoder-standard"></a>Migrowanie do standardu kodera multimediów

Oto typowy przykład kodu języka C#, który używa starszego procesora multimediów. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Oto zaktualizowana wersja, która używa Media Encoder Standard.

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

Jeśli utworzono własne ustawienie kodowania dla pliku AME przy użyciu jego schematu, istnieje [równoważny schemat dla programu Media Encoder Standard](media-services-mes-schema.md). Jeśli masz pytania dotyczące mapowania starszych ustawień na nowy koder, skontaktuj się z nami za pośrednictwemmailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Znane różnice 

Media Encoder Standard jest bardziej wytrzymały, niezawodny, ma lepszą wydajność i zapewnia lepszą jakość wydruku niż starszy koder AME. Ponadto: 

* Media Encoder Standard tworzy pliki wyjściowe z inną konwencją nazewnictwa niż AME.
* Media Encoder Standard produkuje artefakty, takie jak pliki zawierające [metadane pliku wejściowego](media-services-input-metadata-schema.md) i [metadane plików wyjściowych](media-services-output-metadata-schema.md).

## <a name="next-steps"></a>Następne kroki

* [Starsze składniki](legacy-components.md)
* [Strona cennika](https://azure.microsoft.com/pricing/details/media-services/#encoding)
