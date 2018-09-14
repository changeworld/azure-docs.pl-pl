---
title: Pojęcia dotyczące usługi Azure indeksator wideo | Dokumentacja firmy Microsoft
description: W tym temacie opisano niektóre pojęcia związane z usługą Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 67bbb7046d71b904dfdcaad0e44024e8f30ac288
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543712"
---
# <a name="video-indexer-concepts"></a>Wideo pojęcia indeksatora
 
W tym artykule opisano niektóre pojęcia związane z usługą Video Indexer.
    
## <a name="summarized-insights"></a>Informacje podsumowania

Skrócona szczegółowe informacje zawierają zagregowany widok danych: tematy twarzy, emocji. Na przykład zamiast przechodzenia przez każdy z tysiącami zakresów czasu i sprawdzanie twarzy, które są w nim, podsumowane szczegółowe informacje zawiera wszystkich powierzchni, i dla każdego z nich, zakresów czasu, w którym występuje i % czasu jest wyświetlany.

## <a name="time-range-vs-adjusted-time-range"></a>zakres czasu, a zakres czasu skorygowany

TimeRange jest zakres czasu w oryginalnego filmu wideo. AdjustedTimeRange jest zakres czasu, względem bieżącej listy odtwarzania. Ponieważ można utworzyć listę odtwarzania w różnych wierszach różnych plików wideo, możesz podjąć wideo 1-godzinnego i tylko 1 w wierszu polecenia z niego, na przykład 10:00-10:15. W takim przypadku konieczne będzie lista odtwarzania z 1 wiersz, gdzie zakres czasu jest 10:00-10:15, ale adjustedTimeRange to 00:00-00:15.
 
## <a name="blocks"></a>bloki

Bloki są przeznaczone do ułatwienia przechodzą przez dane. Na przykład blok może można podzielić na podstawie prelegentów, zmienić lub istnieje długa wstrzymania.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje o tym, jak rozpocząć pracę, zobacz [jak się zarejestrować i Przekaż swoje pierwsze wideo](video-indexer-get-started.md).

## <a name="see-also"></a>Zobacz także

[Omówienie indeksatora wideo](video-indexer-overview.md)
