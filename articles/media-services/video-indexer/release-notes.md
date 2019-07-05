---
title: Usługa Azure Media Services Video Indexer — informacje o wersji | Dokumentacja firmy Microsoft
description: Aby uzyskać najnowsze informacje o najnowszych zmianach, ten artykuł zawiera najnowsze aktualizacje w usłudze Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: f1c5f43316292f17547b84d970a0f03a1a2c366f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454024"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Usługa Azure Media Services Video Indexer — informacje o wersji

Aby uzyskać najnowsze informacje o najnowszych zmianach, w tym artykule przedstawiono informacje o:

* Zainstalowane najnowsze wersje
* Znane problemy
* Poprawki błędów
* Przestarzałe funkcje

## <a name="june-2019"></a>2019 czerwca

### <a name="video-indexer-deployed-to-japan-east"></a>Usługa Video Indexer wdrożony w regionie Japonia Wschodnia

Teraz można utworzyć Video Indexer płatne konto w regionie Japonia Wschodnia.

### <a name="create-and-repair-account-api-preview"></a>Tworzenie i napraw konta interfejsu API (wersja zapoznawcza)

Dodano nowy interfejs API, który pozwala na [zaktualizować punkt końcowy połączenia usługi Azure Media Service lub klucz](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Poprawa obsługi podczas przekazywania błędów 

Opisowy komunikat jest zwracany w przypadku błędnej konfiguracji podstawowego konta usługi Azure Media Services.

### <a name="player-timeline-keyframes-preview"></a>Odtwarzacz osi czasu klatek kluczowych (wersja zapoznawcza) 

Podgląd obrazu za każdym razem będą teraz widoczne w odtwarzaczu na osi czasu.

### <a name="editor-semi-select"></a>Edytor częściowego wyboru

Można teraz wyświetlić podgląd wszystkie szczegółowe informacje, które są wybrane w wyniku Wybieranie przedziału czasu określonego wgląd w edytorze.

## <a name="may-2019"></a>Maja 2019 r

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aktualizowanie języka niestandardowego modelu z pliku napisów

[Utwórz język niestandardowy model](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) i [aktualizacji niestandardowych modeli językowych](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) interfejsy API obsługują teraz VTT, SRT, i formaty plików TTML jako dane wejściowe dla modeli językowych.

Podczas wywoływania [transkrypcji wideo aktualizacji interfejsu API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript), transkrypcję jest automatycznie dodawany. Uczenia modeli skojarzone z filmu wideo jest także automatycznie aktualizowane. Aby uzyskać informacje na temat sposobu dostosowywania i uczenie modeli językowych, zobacz [dostosować model języka, za pomocą indeksatora wideo](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nowy format transkrypcji pobierania — TXT i woluminów CSV

Oprócz zamknięte podpisów format już obsługiwane (SRT VTT i TTML) Video Indexer obsługuje teraz, pobierając transkrypcji w TXT i CSV.

## <a name="next-steps"></a>Kolejne kroki

[Omówienie](video-indexer-overview.md)
