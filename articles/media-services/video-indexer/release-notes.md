---
title: Informacje o wersji Azure Media Services Video Indexer | Microsoft Docs
description: Aby zachować aktualność najnowszych zmian, ten artykuł zawiera najnowsze aktualizacje Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 07/22/2019
ms.author: juliako
ms.openlocfilehash: fd67e867b5eac58838551ac29e1fca5e860b15ff
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414214"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Informacje o wersji Azure Media Services Video Indexer

Aby uzyskać najnowsze informacje o najnowszych zmianach, w tym artykule przedstawiono informacje o:

* Zainstalowane najnowsze wersje
* Znane problemy
* Poprawki błędów
* Przestarzałe funkcje

## <a name="june-2019"></a>Czerwiec 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer wdrożone na Japonia Wschodnia

Teraz można utworzyć konto płatne Video Indexer w regionie Japonia Wschodnia.

### <a name="create-and-repair-account-api-preview"></a>Tworzenie i naprawa interfejsu API konta (wersja zapoznawcza)

Dodano nowy interfejs API, który umożliwia [zaktualizowanie punktu końcowego lub klucza połączenia usługi Azure Media Service](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Ulepsz obsługę błędów podczas przekazywania 

Komunikat opisowy jest zwracany w przypadku nieprawidłowych konfiguracji bazowego konta Azure Media Services.

### <a name="player-timeline-keyframes-preview"></a>Klatki kluczowe programu Player — wersja zapoznawcza 

Możesz teraz zobaczyć podgląd obrazu za każdym razem na osi czasu odtwarzacza.

### <a name="editor-semi-select"></a>Wybór częściowo edytora

Teraz można wyświetlić podgląd wszystkich szczegółowych informacji, które są wybrane w wyniku wybrania konkretnego przedziału czasu wglądu w edytorze.

## <a name="may-2019"></a>2019 maja

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aktualizowanie niestandardowego modelu języka z pliku napisów

[Tworzenie niestandardowego modelu języka](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) i [Aktualizowanie niestandardowych modeli języka](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) interfejsy API obsługują teraz formaty plików VTT, narzędzia SRT i ttml jako dane wejściowe dla modeli języka.

Podczas wywoływania [interfejsu API aktualizowania transkrypcji wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)transkrypcja jest dodawana automatycznie. Model szkoleniowy skojarzony z filmem wideo jest również aktualizowany automatycznie. Aby uzyskać informacje na temat dostosowywania i uczenia modeli języka, zobacz [Dostosowywanie modelu języka za pomocą Video Indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nowe formaty pobierania transkrypcji — TXT i CSV

Oprócz obsługiwanego formatu napisów (narzędzia SRT, VTT i TTML), Video Indexer teraz obsługuje pobieranie transkrypcji w formatach TXT i CSV.

## <a name="next-steps"></a>Następne kroki

[Omówienie](video-indexer-overview.md)
