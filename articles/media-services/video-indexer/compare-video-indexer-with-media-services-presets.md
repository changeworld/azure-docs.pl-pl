---
title: Porównanie ustawień wstępnych Video Indexer i Azure Media Services v3 | Microsoft Docs
description: W tym artykule porównano możliwości Video Indexer i Azure Media Services ustawień wstępnych v3.
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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 055e210efbb01268654e7823a0fc1320e9c27646
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892825"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Porównanie ustawień wstępnych Azure Media Services v3 i Video Indexer 

W tym artykule porównano możliwości **interfejsów api Video Indexer** i **interfejsów API Media Services v3**. 

Obecnie istnieje nakładanie się między funkcjami oferowanymi przez [interfejsy api Video Indexer](https://api-portal.videoindexer.ai/) i [interfejsy API Media Services v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). W poniższej tabeli przedstawiono bieżące wytyczne dotyczące interpretacji różnic i podobieństw. 

## <a name="compare"></a>Porównaj

|Funkcja|Interfejsy API Video Indexer |Analizator wideo i ustawienia wstępne analizatora dźwięku<br/>w interfejsach API Media Services v3|
|---|---|---|
|Szczegółowe informacje o multimediach|[Usprawnion](video-indexer-output-json-v2.md) |[Podstawowe założenia](../latest/intelligence-concept.md)|
|Możliwości|Zapoznaj się z pełną listą obsługiwanych funkcji: <br/> [Omówienie](video-indexer-overview.md)|Zwraca tylko wgląd w dane wideo|
|Rozliczenia|[Cennik Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Cennik Media Services](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Zgodność|[Iso 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018), [SOC 1, 2, 3](https://www.microsoft.com/TrustCenter/Compliance/SOC), [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa), [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp), [PCI](https://www.microsoft.com/trustcenter/compliance/pci)i [HiTRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) certyfikowane. Najnowsze aktualizacje można znaleźć w temacie [bieżący stan certyfikatów Video Indexer](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).|Media Services jest zgodny z wieloma certyfikatami. Zapoznaj się z [ofertami zgodności platformy Azure. PDF](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) i wyszukaj ciąg "Media Services", aby sprawdzić, czy jest on zgodny z certyfikatem zainteresowań.|
|Bezpłatna wersja próbna|Wschodnie stany USA|Niedostępne|
|Dostępność w poszczególnych regionach|Wschodnie stany USA 2, Południowo-środkowe stany USA, zachodnie stany USA 2, Europa Północna, Europa Zachodnia, Azja Południowo-Wschodnia, Azja Wschodnia i Australia Wschodnia.  Aby uzyskać najnowsze aktualizacje, odwiedź stronę [produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) .|Zobacz temat [Stan platformy Azure](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Następne kroki

[Omówienie usługi Video Indexer](video-indexer-overview.md)

[Media Services wersja 3 — Omówienie](../latest/media-services-overview.md)
