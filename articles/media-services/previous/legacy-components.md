---
title: Azure Media Services starsze składniki | Microsoft Docs
description: W tym temacie omówiono Azure Media Services starszych składników.
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
ms.date: 10/27/2019
ms.author: juliako
ms.openlocfilehash: 30e8fa51df42d202d77ecdbc6a31fe3c7aaac6f4
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968621"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services starsze składniki

W miarę upływu czasu wprowadzono stałe ulepszenia i ulepszenia składników usługi Media. W związku z tym niektóre starsze składniki zostały wycofane. Możesz znaleźć instrukcje dotyczące sposobu migrowania aplikacji ze starszego składnika do bieżącego składnika w poniższych artykułach.

## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Plany wycofania starszych składników i wskazówki dotyczące migracji

Ogłaszamy przestarzałe procesory multimediów w *systemie Windows Azure Media Encoder* (WAME) i *Azure Media Encoder* (azwa). Te procesory są wycofywane w dniu 30 listopada 2019.

* [Migrowanie z systemu Windows Azure Media Encoder do Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrowanie z Azure Media Encoder do Media Encoder Standard](migrate-azure-media-encoder.md)

Ogłaszamy również wycofanie następujących Media Analytics procesorów multimedialnych: 

|Nazwa procesora multimediów|Data wycofania|Uwagi dodatkowe|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 1 stycznia 2020|Ten procesor multimediów zostanie zastąpiony przez [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Aby uzyskać więcej informacji, zobacz [Migrowanie z Azure Media Indexer 2 do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 października z 2020|Ten procesor multimediów zostanie zastąpiony przez [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Aby uzyskać więcej informacji, zobacz [Migrowanie z Azure Media Indexer do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md)
|[Azure Media Face Detector](media-services-face-and-emotion-detection.md)|1 lutego 2020|Ten Media Analyticsy procesor w wersji zapoznawczej zostanie wycofany i nie zostanie przeniesiony do ogólnej dostępności. Oceniamy swoje scenariusze i przypadki użycia z klientami na potrzeby przyszłych inwestycji.|
|[Azure Media Motion Detector](media-services-motion-detection.md)|1 lutego 2020|Ten Media Analyticsy procesor w wersji zapoznawczej zostanie wycofany i nie zostanie przeniesiony do ogólnej dostępności. Oceniamy swoje scenariusze i przypadki użycia z klientami na potrzeby przyszłych inwestycji.|
|[Usługa Azure Media OCR](media-services-video-optical-character-recognition.md)|1 lutego 2020|Ten procesor multimediów zostanie zastąpiony przez [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) i [wstępnie ustawioną Analizator wideo Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md).|
|[Azure Media Video Thumbnails](media-services-video-summarization.md)|1 lutego 2020|Ten procesor multimediów zostanie zastąpiony przez [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) i [wstępnie ustawioną Analizator wideo Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md).|

## <a name="next-steps"></a>Następne kroki

[Wskazówki dotyczące migracji dotyczące przenoszenia z Media Services V2 do wersji v3](../latest/migrate-from-v2-to-v3.md)
