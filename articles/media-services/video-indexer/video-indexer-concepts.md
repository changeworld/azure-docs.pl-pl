---
title: Wideo pojęcia indeksatora
titlesuffix: Azure Media Services
description: W tym temacie opisano niektóre pojęcia związane z usługą Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: fde98dac45eeda9ec2c842b9d8e0f93134ab8092
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292336"
---
# <a name="video-indexer-concepts"></a>Wideo pojęcia indeksatora
 
W tym artykule opisano niektóre pojęcia związane z usługą Video Indexer.
    
## <a name="summarized-insights"></a>Informacje podsumowania

Skrócona szczegółowe informacje zawierają zagregowany widok danych: tematy twarzy, emocji. Na przykład zamiast przechodzenia przez każdy z tysiącami zakresów czasu i sprawdzanie twarzy, które są w nim, podsumowane szczegółowe informacje zawiera wszystkich powierzchni, i dla każdego z nich, zakresów czasu, w którym występuje i % czasu jest wyświetlany.

## <a name="time-range-vs-adjusted-time-range"></a>zakres czasu, a zakres czasu skorygowany

TimeRange jest zakres czasu w oryginalnego filmu wideo. AdjustedTimeRange jest zakres czasu, względem bieżącej listy odtwarzania. Ponieważ można utworzyć listę odtwarzania w różnych wierszach różnych plików wideo, możesz podjąć wideo 1-godzinnego i tylko 1 w wierszu polecenia z niego, na przykład 10:00-10:15. W takim przypadku konieczne będzie lista odtwarzania z 1 wiersz, gdzie zakres czasu jest 10:00-10:15, ale adjustedTimeRange to 00:00-00:15.
 
## <a name="blocks"></a>bloki

Bloki są przeznaczone do ułatwienia przechodzą przez dane. Blok może zostać wyodrębniony na przykład na podstawie zmiany osoby mówiącej lub wystąpienia długiej przerwy.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje o tym, jak rozpocząć pracę, zobacz [jak się zarejestrować i Przekaż swoje pierwsze wideo](video-indexer-get-started.md).

## <a name="see-also"></a>Zobacz także

[Omówienie usługi Video Indexer](video-indexer-overview.md)
