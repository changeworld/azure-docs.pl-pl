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
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: d4f4cfc005b2d5a63512245baee5230e9a26cb37
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309209"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services starsze składniki

W miarę upływu czasu wprowadzono stałe ulepszenia i ulepszenia składników usługi Media. W związku z tym niektóre starsze składniki zostały wycofane. Możesz znaleźć instrukcje dotyczące sposobu migrowania aplikacji ze starszego składnika do bieżącego składnika w poniższych artykułach.

## <a name="legacy-components-and-migration-guidance"></a>Starsze składniki i wskazówki dotyczące migracji

Ogłaszamy przestarzałe procesory multimediów w *systemie Windows Azure Media Encoder* (WAME) i *Azure Media Encoder* (azwa). Te procesory są wycofywane w dniu 30 listopada 2019.

* [Migrowanie z systemu Windows Azure Media Encoder do Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrowanie z Azure Media Encoder do Media Encoder Standard](migrate-azure-media-encoder.md)

Ogłaszamy również przestarzałe *Azure Media Indexer* i *Azure Media Indexer 2*. Procesor multimediów [Azure Media Indexer](media-services-index-content.md) zostanie wycofany 1 października 2020. Procesory multimediów [[Azure Media Indexer 2 (wersja zapoznawcza)](media-services-process-content-with-indexer2.md) zostaną wycofane z dniem 1 stycznia 2019.  [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) zastępuje te starsze procesory nośników.

* [Przeprowadź migrację z Azure Media Indexer i Azure Media Indexer 2 do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

## <a name="next-steps"></a>Następne kroki

[Wskazówki dotyczące migracji dotyczące przenoszenia z Media Services V2 do wersji v3](../latest/migrate-from-v2-to-v3.md)
