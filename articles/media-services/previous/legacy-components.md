---
title: Starsze składniki usługi Azure Media Services | Dokumenty firmy Microsoft
description: W tym temacie omówiono starsze składniki usługi Azure Media Services.
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
ms.date: 02/27/2020
ms.author: juliako
ms.openlocfilehash: 94a70a1234d902787f248890f0cb538a4ba9c2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921083"
---
# <a name="azure-media-services-legacy-components"></a>Starsze składniki usługi Azure Media Services

Z biegiem czasu nastąpiły stałe ulepszenia i ulepszenia składników usługi Media Service. W rezultacie niektóre starsze składniki zostały wycofane. Instrukcje dotyczące migracji aplikacji ze starszego składnika do bieżącego składnika można znaleźć w następujących artykułach.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Plany emerytalne starszych składników i wytyczne dotyczące migracji

Ogłaszamy wycofanie procesorów *multimediów Windows Azure Media Encoder* (WAME) i Azure Media *Encoder* (AME). Procesory te zostaną wycofane 31 marca 2020 roku.

* [Migrowanie z programu Windows Azure Media Encoder do media encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrowanie z usługi Azure Media Encoder do media encoder Standard](migrate-azure-media-encoder.md)

Ogłaszamy również wycofanie następujących procesorów multimedialnych Media Analytics: 
 
|Nazwa procesora multimediów|Data przejścia na emeryturę|Uwagi dodatkowe|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1 stycznia 2020|Ten procesor multimediów jest zastępowany przez [indeksator wideo usługi Azure Media Services](https://docs.microsoft.com/azure/media-services/video-indexer/). Aby uzyskać więcej informacji, zobacz [Migrowanie z indeksatora multimediów azure 2 do indeksatora wideo usługi Azure Media Services](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 marca 2023 r.|Ten procesor multimediów jest zastępowany przez [indeksator wideo usługi Azure Media Services](https://docs.microsoft.com/azure/media-services/video-indexer/). Aby uzyskać więcej informacji, zobacz [Migrowanie z indeksatora multimediów platformy Azure do indeksatora wideo usługi Azure Media Services](migrate-indexer-v1-v2.md)|
|[Wykrywanie ruchu](media-services-motion-detection.md)|1 czerwca 2020|W tej chwili nie ma planów wymiany.|
|[Podsumowanie filmu](media-services-video-summarization.md)|1 czerwca 2020|W tej chwili nie ma planów wymiany.|
|[Optyczne rozpoznawanie znaków wideo](media-services-video-optical-character-recognition.md)|1 czerwca 2020|Ten procesor multimediów jest zastępowany przez [indeksator wideo usługi Azure Media Services](https://docs.microsoft.com/azure/media-services/video-indexer/). Należy również rozważyć użycie [interfejsu API usługi Azure Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Zobacz [Porównanie ustawień predefiniowanych usługi Azure Media Services w wersji 3 i indeksatora wideo](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Wykrywanie twarzy](media-services-face-and-emotion-detection.md)|1 czerwca 2020|Ten procesor multimediów jest zastępowany przez [indeksator wideo usługi Azure Media Services](https://docs.microsoft.com/azure/media-services/video-indexer/). Należy również rozważyć użycie [interfejsu API usługi Azure Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Zobacz [Porównanie ustawień predefiniowanych usługi Azure Media Services w wersji 3 i indeksatora wideo](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Moderator zawartości](media-services-content-moderation.md)|1 czerwca 2020|Ten procesor multimediów jest zastępowany przez [indeksator wideo usługi Azure Media Services](https://docs.microsoft.com/azure/media-services/video-indexer/). Należy również rozważyć użycie [interfejsu API usługi Azure Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Zobacz [Porównanie ustawień predefiniowanych usługi Azure Media Services w wersji 3 i indeksatora wideo](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|

## <a name="next-steps"></a>Następne kroki

[Wskazówki dotyczące migracji dotyczące przechodzenia z usługi Media Services w wersji 2 do wersji 3](../latest/migrate-from-v2-to-v3.md)
