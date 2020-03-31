---
title: Analiza transmisji na żywo przy użyciu indeksatora wideo
titleSuffix: Azure Media Services
description: W tym artykule pokazano, jak przeprowadzić analizę strumienia na żywo przy użyciu indeksatora wideo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186000"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Analiza transmisji na żywo za pomocą indeksatora wideo

Indeksator wideo usługi Azure Media Services to usługa platformy Azure zaprojektowana w celu wyodrębniania szczegółowych informacji z plików wideo i audio w trybie offline. Ma to na celu przeanalizowanie danego pliku multimedialnego już utworzonego z wyprzedzeniem. Jednak w niektórych przypadkach użycia ważne jest, aby uzyskać wgląd w media z kanału na żywo tak szybko, jak to możliwe, aby odblokować operacyjne i inne przypadki użycia wciśnięty w czasie. Na przykład takie bogate metadane w strumieniu na żywo mogą być używane przez producentów treści do automatyzacji produkcji telewizyjnej.

Rozwiązanie opisane w tym artykule umożliwia klientom korzystanie z indeksatora wideo w niemal rozdzielczościach w czasie rzeczywistym w kanałach na żywo. Opóźnienie w indeksowaniu może być tak niskie, jak cztery minuty przy użyciu tego rozwiązania, w zależności od fragmentów indeksowanych danych, rozdzielczość danych wejściowych, typ zawartości i oparte na obliczeniach używane dla tego procesu.

![Metadane indeksatora wideo w strumieniu na żywo](./media/live-stream-analysis/live-stream-analysis01.png)

*Rysunek 1 — przykładowy odtwarzacz wyświetlający metadane indeksatora wideo w strumieniu na żywo*

[Rozwiązanie do analizy strumienia](https://aka.ms/livestreamanalysis) pod ręką, używa usługi Azure Functions i dwie aplikacje logiki do przetwarzania programu na żywo z kanału na żywo w usłudze Azure Media Services za pomocą indeksatora wideo i wyświetla wynik z usługi Azure Media Player pokazujący strumień w czasie zbliżonym do rzeczywistego.

Na wysokim poziomie składa się z dwóch głównych etapów. Pierwszy krok jest uruchamiany co 60 sekund i wykonuje podpoślizg z ostatnich 60 sekund odtwarzania, tworzy z niego zasób i indeksuje go za pośrednictwem indeksatora wideo. Następnie drugi krok jest wywoływana po zakończeniu indeksowania. Przechwycone szczegółowe informacje są przetwarzane, wysyłane do usługi Azure Cosmos DB, a indeksowany subclip jest usuwany.

Przykładowy odtwarzacz odtwarza strumień na żywo i pobiera szczegółowe informacje z usługi Azure Cosmos DB przy użyciu dedykowanej funkcji platformy Azure. Wyświetla metadane i miniatury zsynchronizowane z wideo na żywo.

![Dwie aplikacje logiczne przetwarzają transmisję na żywo co minutę w chmurze](./media/live-stream-analysis/live-stream-analysis02.png)

*Rysunek 2 — dwie aplikacje logiki przetwarzania transmisji na żywo co minutę w chmurze.*

## <a name="step-by-step-guide"></a>Przewodnik krok po kroku 

Pełny kod i przewodnik krok po kroku, aby wdrożyć wyniki można znaleźć w [projekcie GitHub dla analizy multimediów na żywo z Video Indexer](https://aka.ms/livestreamanalysis). 

## <a name="next-steps"></a>Następne kroki

[Omówienie usługi Video Indexer](video-indexer-overview.md)
