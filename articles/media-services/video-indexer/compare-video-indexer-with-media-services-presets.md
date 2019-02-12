---
title: Porównanie Video Indexer i usługi Azure Media Services v3 ustawień wstępnych | Dokumentacja firmy Microsoft
description: W tym temacie porównano Video Indexer i usługa Azure Media Services v3 wstępne.
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: b4c74a67c66b12e3a7333f3e679e7c61a750cf7e
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2019
ms.locfileid: "56002395"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Porównanie usług Azure Media Services v3 wstępne i Video Indexer 

W tym artykule porównano możliwości **interfejsów API indeksatora wideo** i **usługi Media Services v3 API**. 

Obecnie nakładają się na funkcji oferowanych przez [interfejsów API w wersji 2 Video Indexer](https://api-portal.videoindexer.ai/) i [usługi Media Services v3 API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). Poniższa tabela zawiera bieżące wytyczne dla zrozumienia różnice i podobieństwa. 

## <a name="compare"></a>Porównanie

|Cecha|Interfejsy API indeksatora wideo |Analizator wideo i Audio analizatora wstępne<br/>w interfejsy API usług Media Services v3|
|---|---|---|
|Media Insights|[Rozszerzone](video-indexer-output-json-v2.md) |[Podstawy](../latest/intelligence-concept.md)|
|Środowiska|Zobacz pełną listę obsługiwanych funkcji: <br/> [Omówienie](video-indexer-overview.md)|Zwraca tylko informacjom o filmie wideo|
|Rozliczenia|[Cennika usługi Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Cennika usługi Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Zgodność|[Zgodność platformy Azure](https://aka.ms/AzureCompliance)|Usługa Media Services jest zgodna z wielu certyfikatów. Zapoznaj się z [Offerings.pdf zgodności platformy Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) i poszukaj pozycji "Media Services" zobaczyć, jeśli jest on zgodny z certyfikatem zainteresowania.|
|Bezpłatna wersja próbna|Wschodnie stany USA|Niedostępne|
|Dostępność |Zachodnie stany USA, Azja Wschodnia, Europa Północna|Zobacz [stanu platformy Azure](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Kolejne kroki

[Omówienie usługi Video Indexer](video-indexer-overview.md)

[Przegląd usługi Media Services v3](../latest/media-services-overview.md)
