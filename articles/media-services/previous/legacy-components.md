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
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 592fd8901fc7f8a82a390f3c125c712ef5829a52
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083712"
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

## <a name="next-steps"></a>Następne kroki

[Wskazówki dotyczące migracji dotyczące przenoszenia z Media Services V2 do wersji v3](../latest/migrate-from-v2-to-v3.md)
