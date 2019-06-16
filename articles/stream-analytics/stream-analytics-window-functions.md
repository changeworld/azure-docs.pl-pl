---
title: Wprowadzenie do funkcji obsługi okien usługi Azure Stream Analytics
description: W tym artykule opisano cztery funkcji obsługi okien (wirowania przeskokiem, przesuwanie, "sesja"), które są używane w zadaniach usługi Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 56b6f11d226f25e3094a90d8646fa13860ee306e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066759"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Wprowadzenie do funkcji obsługi okien usługi Stream Analytics

W scenariuszach z przesyłania strumieniowego w czasie wykonywania operacji na danych znajdujących się na danych czasowych systemu windows jest to typowy wzorzec. Stream Analytics zapewnia natywną obsługę funkcji obsługi okien, dzięki czemu deweloperzy mogą zadania przetwarzania strumieniowego złożonych Autor przy minimalnym nakładzie pracy.

Istnieją cztery rodzaje danych czasowych systemu windows do wyboru: [**Wirowania**](https://msdn.microsoft.com/azure/stream-analytics/reference/tumbling-window-azure-stream-analytics), [ **przeskokiem**](https://msdn.microsoft.com/azure/stream-analytics/reference/hopping-window-azure-stream-analytics), [ **kroczącym**](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics), i [ **sesji**  ](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics) systemu windows.  Korzystanie z funkcji okna, które w [ **GROUP BY** ](https://msdn.microsoft.com/azure/stream-analytics/reference/group-by-azure-stream-analytics) klauzuli składni zapytania w zadaniach usługi Stream Analytics. Możesz także agregować zdarzenia za pośrednictwem wielu systemu windows za pomocą [ **Windows()** funkcja](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics).

Wszystkie [obsługi okien](https://msdn.microsoft.com/azure/stream-analytics/reference/windowing-azure-stream-analytics) operacji dane wyjściowe w **zakończenia** okna. Dane wyjściowe okna będzie pojedyncze zdarzenie oparte na funkcji agregującej używane. Zdarzenia danych wyjściowych będzie mieć sygnaturę czasową koniec okna i wszystkie funkcje okna są zdefiniowane o stałej długości. 

![Pojęcia dotyczące funkcji Stream Analytics okna](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Okna wirowania
Wirowania okno, które funkcje są używane do segmentu strumień danych do czasu odrębnych segmentach i wykonywania funkcji, takich jak w poniższym przykładzie. Kluczowych różnic okna wirowania to, że ich powtórzyć, czy nie nakładają się i zdarzenia nie może należeć do więcej niż jedno okno wirowania.

![Okno wirowania usługi Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Okno przeskoku
Przeskoku okna funkcje przeskok do przodu w czasie przez stały okres. Może to być łatwe do pełnią one okna wirowania, które można zachodziły na siebie, więc zdarzenia mogą należeć do więcej niż jeden zestaw wyników okno Hopping. W oknie Hopping taka sama jak okna wirowania, ustaw rozmiar przeskoku być taki sam jak rozmiar okna. 

![Okno przeskoku usługi Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Przesuwającego się okna
Funkcje okna przewijania, w odróżnieniu od wirowania lub przeskokiem systemu windows, wygenerowanie danych wyjściowych **tylko** po wystąpieniu zdarzenia. Każdego okna będzie zawierać co najmniej jedno zdarzenie, a okno stale przesuwa do przodu € (epsilon). Podobnie jak przeskokiem systemu windows, zdarzenia mogą należeć do więcej niż jeden przesuwającego się okna.

![Stream Analytics przesuwającego się okna](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Okno w sesji
Funkcje okien sesji grupie zdarzenia odbierane w czasie podobne, filtrowanie czasu w przypadku, gdy nie ma żadnych danych. Ma trzy główne parametrów: limit czasu, maksymalny czas trwania, a klucz partycji (opcjonalny).

![Okna sesji usługi Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Okna sesji rozpoczyna się, gdy wystąpi pierwsze zdarzenie. Jeśli inne zdarzenie występuje przed upływem określonego limitu czasu z ostatnie zdarzenie odebrane, okno rozszerza obejmują nowe zdarzenie. W przeciwnym razie jeśli żadne zdarzenia nie występują przed upływem limitu czasu, okno jest zamknięty na limit czasu.

Jeśli zdarzenia zachować przed upływem określonego limitu czasu, okna sesji będzie przechowywać rozszerzanie aż do osiągnięcia maksymalnego czasu trwania. Maksymalny czas trwania sprawdzania odstępach czasu są ustawiane jako taki sam rozmiar jak określony maksymalny czas trwania. Na przykład, jeśli maksymalny czas trwania wynosi 10, a następnie kontroli, gdy okno przekracza maksymalny czas trwania będzie możliwe w t = 0, 10, 20, 30, itp.

Jeśli zostanie podany klucz partycji, zdarzenia są grupowane według klucza i okna sesji jest stosowana do każdej grupy niezależnie. Ta Partycjonowanie jest przydatne w sytuacjach wymagających innej sesji systemu windows dla różnych użytkowników lub urządzeń.


## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

