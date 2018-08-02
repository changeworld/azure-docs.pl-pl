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
ms.openlocfilehash: 224c8b05027f51fb99c8d58be34c3604032c0f77
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399499"
---
# <a name="video-indexer-concepts"></a>Wideo pojęcia indeksatora
 
W tym temacie opisano niektóre pojęcia związane z usługą Video Indexer.
    
## <a name="summarized-insights"></a>Informacje podsumowania

Skrócona szczegółowe informacje zawierają zagregowany widok danych: twarzy, słowa kluczowe, tonacji. Na przykład zamiast przechodzenia przez każdy z tysiącami zakresów czasu i sprawdzanie twarzy, które są w nim, podsumowane szczegółowe informacje zawiera wszystkich powierzchni, i dla każdego z nich, zakresów czasu, w którym występuje i % czasu jest wyświetlany.

## <a name="topicskeywords"></a>Tematy dotyczące/słów kluczowych

Tematy dotyczące/słowa kluczowe są na liście kluczowych fraz, które Video Indexer wyodrębnia z tekstu. Na przykład klip wideo Scotta Guthriego może zawierać następujące tematy/słowa kluczowe: zabezpieczeń, Azure, Microsoft Cloud, przychodów.

## <a name="sentiments"></a>opinie

Gdy indeksator wideo analizuje zapisy, wykrywa także tonacji. Na przykład "jest bardzo ekscytująca zdarzenia" jest pozytywną tonację.

## <a name="time-range-vs-adjusted-time-range"></a>zakres czasu, a zakres czasu skorygowany

TimeRange jest zakres czasu w oryginalnego filmu wideo. AdjustedTimeRange jest zakres czasu, względem bieżącej listy odtwarzania. Ponieważ można utworzyć listę odtwarzania w różnych wierszach różnych plików wideo, możesz podjąć wideo 1-godzinnego i tylko 1 w wierszu polecenia z niego, na przykład 10:00-10:15. W takim przypadku konieczne będzie lista odtwarzania z 1 wiersz, gdzie zakres czasu jest 10:00-10:15, ale adjustedTimeRange to 00:00-00:15.
 
## <a name="blocks"></a>bloki

Bloki są przeznaczone do ułatwienia przechodzą przez dane. Na przykład blok może można podzielić na podstawie prelegentów, zmienić lub istnieje długa wstrzymania.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje o tym, jak rozpocząć pracę, zobacz [jak się zarejestrować i Przekaż swoje pierwsze wideo](video-indexer-get-started.md).

## <a name="see-also"></a>Zobacz także

[Omówienie indeksatora wideo](video-indexer-overview.md)
