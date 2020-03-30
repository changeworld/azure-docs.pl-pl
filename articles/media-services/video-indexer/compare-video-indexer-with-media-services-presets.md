---
title: Porównanie ustawień predefiniowanych indeksatora wideo i usługi Azure Media Services w wersji 3
description: W tym artykule porównano możliwości indeksatora wideo i ustawienia predefiniowane usługi Azure Media Services w wersji 3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: dcfc6ea4afe23424e72c625518356be52f62bc81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602192"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Porównanie ustawień predefiniowanych usługi Azure Media Services w wersji 3 i indeksatora wideo 

W tym artykule porównano możliwości **interfejsów API indeksatora wideo** i **interfejsów API usługi Media Services w wersji 3**. 

Obecnie istnieje nakładanie się funkcji oferowanych przez [interfejsy API indeksatora](https://api-portal.videoindexer.ai/) wideo i [interfejsów API usługi Media Services w wersji 3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). W poniższej tabeli przedstawiono aktualne wytyczne dotyczące zrozumienia różnic i podobieństw. 

## <a name="compare"></a>Porównanie

|Funkcja|Interfejsy API indeksatora wideo |Predefiniatory analizatora wideo i analizatora dźwięku<br/>w interfejsach API usługi Media Services w wersji 3|
|---|---|---|
|Wgląd w media|[Rozszerzone](video-indexer-output-json-v2.md) |[Podstawy](../latest/intelligence-concept.md)|
|Doświadczenia|Zobacz pełną listę obsługiwanych funkcji: <br/> [Przegląd](video-indexer-overview.md)|Zwraca tylko szczegółowe informacje wideo|
|Rozliczenia|[Ceny usług multimedialnych](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Ceny usług multimedialnych](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Zgodność|Aby uzyskać najnowsze aktualizacje zgodności, odwiedź [stronę Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) i wyszukaj hasło "Indeksator wideo", aby sprawdzić, czy jest on zgodny z certyfikatem zainteresowania.|Aby uzyskać najnowsze aktualizacje zgodności, odwiedź [stronę Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) i wyszukaj hasło "Media Services", aby sprawdzić, czy jest ona zgodna z interesującym certyfikatem.|
|Bezpłatna wersja próbna|Wschodnie stany USA|Niedostępne|
|Dostępność w danym regionie|Zobacz [Dostępność usług Cognitive Services według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|Zobacz [Dostępność usługi Media Services według regionu](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Następne kroki

[Omówienie usługi Video Indexer](video-indexer-overview.md)

[Omówienie usługi Media Services w wersji 3](../latest/media-services-overview.md)
