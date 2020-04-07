---
title: Wprowadzenie do funkcji okienowania usługi Azure Stream Analytics
description: W tym artykule opisano cztery funkcje okna (tumbling, przeskakujące, przesuwne, sesji), które są używane w zadaniach usługi Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 872eec62e7a629d76533aa6c9906cbdb64c32236
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745561"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Wprowadzenie do funkcji okienowania usługi Stream Analytics

W scenariuszach przesyłania strumieniowego czasu wykonywanie operacji na danych zawartych w oknach czasowych jest typowym wzorcem. Usługa Stream Analytics obsługuje funkcje okien, umożliwiając deweloperom tworzenie złożonych zadań przetwarzania strumienia przy minimalnym wysiłku.

Istnieją cztery rodzaje okien czasowych do wyboru: [**Tumbling**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics), [**Hopping**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics), [**Przesuwne**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)i [**okna sesji.**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics)  Funkcje okna w klauzuli [**GROUP BY**](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) składni kwerendy w zadaniach usługi Stream Analytics. Zdarzenia można również agregować w wielu oknach za pomocą funkcji [ **Windows().** ](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics)

Wszystkie wyniki [wyjściowe](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) operacji okna na **końcu** okna. Dane wyjściowe okna będzie pojedyncze zdarzenie oparte na funkcji agregacji używane. Zdarzenie wyjściowe będzie miało sygnaturę czasową końca okna, a wszystkie funkcje okna są zdefiniowane ze stałą długością. 

![Pojęcia dotyczące funkcji okien usługi usługi Usługi Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Okno tumbling
Funkcje okna tumbling są używane do segmentowania strumienia danych do różnych segmentów czasu i wykonywania funkcji przeciwko nim, takich jak poniższy przykład. Kluczowe wyróżniki okna wirowania: okna te się powtarzają, nie nakładają się, a zdarzenia nie mogą należeć do więcej niż jednego okna wirowania.

![Okno zawirowań usługi Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Okno hopping
Funkcje okien powtarzanych powodują przeskoki w czasie do przodu o stały okres. Okna te przypominają okna wirowania, ale mogą się nakładać, więc zdarzenia mogą należeć do więcej niż jednego zestawu wyników okien powtarzanych. Aby okno Hopping było takie samo jak okno Tumbling, określ rozmiar przeskoku, który ma być taki sam jak rozmiar okna. 

![Okno przeskakiwania usługi Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Okno przesuwne
Funkcje okna przesuwnego, w przeciwieństwie do okien Tumbling lub Hopping, generują dane wyjściowe **tylko** wtedy, gdy wystąpi zdarzenie. Każde okno będzie miało co najmniej jedno zdarzenie, a okno jest stale przesuwane do przodu przez ε (epsilon). Podobnie jak w przypadku okien powtarzanych, zdarzenia mogą należeć do więcej niż jednego okna przewijania.

![Okno przesuwne usługi Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Okno sesji
Funkcje okna sesji grupują zdarzenia, które docierają o podobnych porach, odfiltrowując okresy czasu, w których nie ma żadnych danych. Okno ma trzy główne parametry: limit czasu, maksymalny czas trwania i klucz partycjonowania (opcjonalny).

![Okno sesji usługi Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Okno sesji rozpoczyna się po wystąpieniu pierwszego zdarzenia. Jeśli inne zdarzenie występuje w określonym limit czasu od ostatniego zdarzenia pochłonięty, a następnie okno rozszerza się o nowe zdarzenie. W przeciwnym razie, jeśli nie wystąpią żadne zdarzenia w przecenie czasu, okno jest zamykane z limitem czasu.

Jeśli zdarzenia nadal występują w określonym limit czasu, okno sesji będzie się rozszerzać aż do osiągnięcia maksymalnego czasu trwania. Maksymalne interwały sprawdzania czasu trwania są ustawione na taki sam rozmiar jak określony maksymalny czas trwania. Na przykład jeśli maksymalny czas trwania wynosi 10, a następnie sprawdza, czy okno przekracza maksymalny czas trwania nastąpi w t = 0, 10, 20, 30, itp.

Po podaniu klucza partycji zdarzenia są grupowane według klucza i okna sesji są stosowane do każdej grupy niezależnie. Ta partycjonowanie jest przydatne w przypadkach, gdy potrzebujesz różnych okien sesji dla różnych użytkowników lub urządzeń.


## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

