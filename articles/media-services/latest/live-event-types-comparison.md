---
title: Azure Media Services w element LiveEvent typy | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono szczegółowe tabelę, która porównywanie typów element LiveEvent.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/27/2018
ms.author: juliako
ms.openlocfilehash: 3ed38ce75e5ee7c9f05533d64b28171482bfec51
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725936"
---
# <a name="liveevent-types-comparison"></a>Porównanie typów element LiveEvent

W usłudze Azure Media Services [element LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) może być jednym z dwóch typów: kodowanie na żywo oraz pass-through. 

## <a name="types-comparison"></a>Porównanie typów 

W poniższej tabeli porównano funkcje dwa typy element LiveEvent.

| Cecha | Element LiveEvent przekazywania | Standardowy element LiveEvent |
| --- | --- | --- |
| Pojedyncza szybkość transmisji bitów w danych wejściowych jest zakodowany do wielokrotnych w chmurze |Nie |Yes |
| Maksymalna rozdzielczość wideo dla udziału kanału informacyjnego |4K (na 60 klatek na sekundę 4096 x 2160) |1080p (1920 x 1088 na 30 klatek na sekundę)|
| Zalecana maksymalna warstwy w udziale kanału informacyjnego|Do 12|Jedną ścieżkę dźwiękową|
| Maksymalna warstwy w danych wyjściowych| Takie same jak dane wejściowe|Do 7|
| Maksymalna przepustowość agregacji wkładu kanału informacyjnego|60 MB/s|ND|
| Maksymalna szybkość transmisji bitów dla pojedynczej warstwy w udziale |20 MB/s|20 MB/s|
| Obsługa wielu języka ścieżki audio|Yes|Nie|
| Obsługiwane wejściowych koderów-dekoderów wideo |Koder H.264/AVC i H.265/HEVC|KODER H.264/AVC|
| Dane wyjściowe obsługiwanych koderów-dekoderów wideo|Takie same jak dane wejściowe|KODER H.264/AVC|
| Obsługiwana głębokość bitów wideo, dane wejściowe i wyjściowe|Maksymalnie 10-bitowy tym HDR 10/HLG|8-bitowa|
| Obsługiwane wejściowe audio kodery-dekodery|AAC-LC, HE-AAC v1, v2 HE AAC|AAC-LC, HE-AAC v1, v2 HE AAC|
| Dane wyjściowe obsługiwanych koderów-dekoderów audio|Takie same jak dane wejściowe|AAC-LC|
| Maksymalna rozdzielczość wideo wyjścia wideo|Takie same jak dane wejściowe|720p (o 30 klatek na sekundę)|
| Protokoły danych wejściowych|RTMP, fragmentacji — w formacie MP4 (Smooth Streaming)|RTMP, fragmentacji — w formacie MP4 (Smooth Streaming)|
| Cena|Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) i kliknij kartę "Wideo na żywo"|Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) i kliknij kartę "Wideo na żywo"|
| Maksymalny czas działania|24 x 365 live liniowego|Całodobowo|
| Możliwość przekazywania napisy kodowane CEA za pośrednictwem osadzonego 608/708 etykiety danych|Yes|Yes|
| Obsługa wstawiania plansz|Nie|Nie|
| Obsługa ad sygnalizowanie za pośrednictwem interfejsu API| Nie|Nie|
| Obsługa ad sygnalizowanie za pośrednictwem SCTE 35 wewnątrzpasmowe wiadomości|Yes|Yes|
| Zdolność odzyskania po krótkie wstrzymania w udziale kanału informacyjnego|Yes|Nie (element LiveEvent rozpocznie się slating ponad 6 sekund bez danych wejściowych)|
| Obsługa niejednolitego GOPs danych wejściowych|Yes|Nie — dane wejściowe muszą naprawiona GOP czas trwania|
| Obsługa dane wejściowe szybkość klatek zmiennej|Yes|Nie — dane wejściowe muszą zostać usunięte, szybkości klatek. Niewielkich zmian są dopuszczalne na przykład podczas sceny wysokiej ruchu. Ale wkład źródła danych nie można porzucić szybkość klatek (na przykład do 15 klatek na sekundę).|
| Auto bliskie z element LiveEvent, gdy dane wejściowe źródła danych zostaną utracone|Nie|Po 12 godzinach, jeśli nie ma żadnych LiveOutput uruchamiania|

## <a name="next-steps"></a>Kolejne kroki

[Omówienie transmisji strumieniowej na żywo](live-streaming-overview.md)
