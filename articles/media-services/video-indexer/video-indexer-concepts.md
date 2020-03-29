---
title: Pojęcia indeksatora wideo
titleSuffix: Azure Media Services
description: W tym artykule opisano niektóre pojęcia usługi indeksatora wideo usługi Azure Media Services.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 73dad1db4f44134f871c9f3d6e7edcdd3bd1e2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900673"
---
# <a name="video-indexer-concepts"></a>Pojęcia indeksatora wideo
 
W tym artykule opisano niektóre pojęcia usługi indeksatora wideo.
    
## <a name="summarized-insights"></a>Podsumowane spostrzeżenia

Podsumowane statystyki zawierają zagregowany widok danych: twarze, tematy, emocje. Na przykład zamiast przechodzić przez każdy z tysięcy zakresów czasu i sprawdzać, które ściany znajdują się w nim, podsumowane spostrzeżenia zawierają wszystkie ściany i dla każdego z nich, zakresy czasu, w których się pojawia, oraz % czasu, w którym jest wyświetlany.

## <a name="time-range-vs-adjusted-time-range"></a>zakres czasu a skorygowany zakres czasu

TimeRange to zakres czasu w oryginalnym filmie. AdjustedTimeRange to zakres czasu względem bieżącej listy odtwarzania. Ponieważ możesz utworzyć listę odtwarzania z różnych linii różnych filmów, możesz zrobić 1-godzinny film i użyć z niego tylko 1 wiersza, na przykład 10:00-10:15. W takim przypadku będziesz mieć listę odtwarzania z 1 linią, gdzie zakres czasu wynosi 10:00-10:15, ale adjustedTimeRange to 00:00-00:15.
 
## <a name="blocks"></a>Bloki

Bloki mają na celu ułatwienie przechodzenia przez dane. Blok może zostać wyodrębniony na przykład na podstawie zmiany osoby mówiącej lub wystąpienia długiej przerwy.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje o tym, jak rozpocząć pracę, zobacz [Jak zarejestrować się i przesłać pierwszy film](video-indexer-get-started.md).

## <a name="see-also"></a>Zobacz też

[Omówienie usługi Video Indexer](video-indexer-overview.md)
