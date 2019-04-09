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
ms.date: 04/07/2019
ms.author: juliako
ms.openlocfilehash: 2c98f6d12f4868e5f90874fe3210fe5368f7ca2d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270340"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Porównanie usług Azure Media Services v3 wstępne i Video Indexer 

W tym artykule porównano możliwości **interfejsów API indeksatora wideo** i **usługi Media Services v3 API**. 

Obecnie nakładają się na funkcji oferowanych przez [interfejsów API indeksatora wideo](https://api-portal.videoindexer.ai/) i [usługi Media Services v3 API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). Poniższa tabela zawiera bieżące wytyczne dla zrozumienia różnice i podobieństwa. 

## <a name="compare"></a>Porównanie

|Cecha|Interfejsy API indeksatora wideo |Analizator wideo i Audio analizatora wstępne<br/>w interfejsy API usług Media Services v3|
|---|---|---|
|Media Insights|[Rozszerzone](video-indexer-output-json-v2.md) |[Podstawy](../latest/intelligence-concept.md)|
|Środowiska|Zobacz pełną listę obsługiwanych funkcji: <br/> [Przegląd](video-indexer-overview.md)|Zwraca tylko informacjom o filmie wideo|
|Rozliczenia|[Cennika usługi Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Cennika usługi Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Zgodność|[ISO 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018), [SOC 1,2,3](https://www.microsoft.com/TrustCenter/Compliance/SOC), [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa), [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp), [PCI](https://www.microsoft.com/trustcenter/compliance/pci)i [ HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) certyfikat. Aby uzyskać najnowsze aktualizacje, odwiedź stronę [bieżący stan certyfikaty usługi Video Indexer](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).|Usługa Media Services jest zgodna z wielu certyfikatów. Zapoznaj się z [Offerings.pdf zgodności platformy Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) i poszukaj pozycji "Media Services" zobaczyć, jeśli jest on zgodny z certyfikatem zainteresowania.|
|Bezpłatna wersja próbna|Wschodnie stany USA|Niedostępne|
|Dostępność w danym regionie|Wschodnie stany USA 2, południowo-środkowe stany USA, zachodnie stany USA 2, Europa Północna, Europa Zachodnia, Azja południowo-wschodnia, Azja Wschodnia i Australia Wschodnia.  Aby uzyskać najnowsze aktualizacje, odwiedź stronę [dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) strony.|Zobacz [stanu platformy Azure](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Kolejne kroki

[Omówienie indeksatora wideo](video-indexer-overview.md)

[Przegląd usługi Media Services v3](../latest/media-services-overview.md)
