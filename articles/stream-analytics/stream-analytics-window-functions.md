---
title: Wprowadzenie do usługi Azure Stream Analytics funkcje okien
description: W tym artykule opisano cztery funkcje okien (wirowania, skaczące, przedłużanie, sesji), które są używane w zadania usługi analiza strumienia Azure.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 2650058e277bc0338c779655ce381be046fb120a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Wprowadzenie do funkcji okien usługi analiza strumienia
W scenariuszach przesyłanie strumieniowe w czasie wykonywania operacji na danych znajdujących się na danych czasowych systemu windows jest wspólnym wzorcem. Analiza strumienia ma macierzystą obsługę funkcji okien umożliwiają deweloperom zadania przetwarzania strumienia złożonych autora przy minimalnym wysiłku.

Istnieją cztery rodzaje danych czasowych systemu windows do wyboru: [ **wirowania**](https://msdn.microsoft.com/azure/stream-analytics/reference/tumbling-window-azure-stream-analytics), [ **Hopping**](https://msdn.microsoft.com/azure/stream-analytics/reference/hopping-window-azure-stream-analytics), [  **Przedłużanie**](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics), i [ **sesji** ](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics) systemu windows.  Użyj funkcji okna w [ **GROUP BY** ](https://msdn.microsoft.com/azure/stream-analytics/reference/group-by-azure-stream-analytics) klauzuli składni zapytania w zadaniach Stream Analytics.

Wszystkie [okienkową](https://msdn.microsoft.com/azure/stream-analytics/reference/windowing-azure-stream-analytics) wyników w danych wyjściowych operacji **zakończenia** okna. Dane wyjściowe okna będą pojedyncze zdarzenie oparte na funkcji agregującej używane. Zdarzenie wyjściowe będą mieć sygnaturę czasową koniec okna i wszystkie funkcje okna są zdefiniowane o stałej długości. 

![Pojęcia dotyczące funkcji okno Analiza strumienia](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Okno wirowania
Wirowania okna, które funkcje są używane do segmentu strumienia danych do czasu odrębnych segmentach i wykonywania funkcji, takich jak w poniższym przykładzie. Klucza wyróżniającymi okna wirowania są, że powtarzają, czy nie nakładają się na siebie, a zdarzenia nie może należeć do więcej niż jedno okno wirowania.

![Okno wirowania analiza strumienia](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Okno przeskoku
Przeskoku okna funkcji przeskok do przodu w czasie w ustalonym okresie. Może to być łatwo traktować ich jako wirowania systemu windows, które mogą nakładać się na, więc zdarzeń mogą należeć do więcej niż jeden zestaw wyników okna Hopping. Aby utworzyć okno Hopping taki sam jak okno wirowania, określ rozmiar przeskoku na taki sam rozmiar okna. 

![Okno przeskoku analiza strumienia](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Na metodzie przesuwanego okna
Funkcje przesuwanego okna, w odróżnieniu od wirowania lub skaczące systemu windows, utworzenie danych wyjściowych **tylko** po wystąpieniu zdarzenia. Co okno będzie zawierać co najmniej jedno zdarzenie i okna stale przenosi do przodu € (epsilon). Podobnie jak skaczące systemu windows, zdarzenia mogą należeć do więcej niż jeden na metodzie przesuwanego okna.

![Analiza strumienia na metodzie przesuwanego okna](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window-preview"></a>Okno sesji (wersja zapoznawcza)
Funkcje okna sesji grupy zdarzenia pojawiające się w czasie podobne filtrowanie okresów w przypadku, gdy nie ma żadnych danych. Składa się z trzech głównych parametry: limit czasu, maksymalny czas trwania i partycjonowania klucza (opcjonalnie).

![Okno sesji usługi analiza strumienia](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Okno sesji rozpoczyna się pierwszego zdarzenia. Jeśli inne zdarzenie wystąpi w ramach określonego limitu czasu z ostatnim zdarzeniem pozyskiwane, okno rozszerza obejmują nowe zdarzenie. W przeciwnym razie jeśli żadne zdarzenia nie pojawiają się w limicie czasu, okno jest zamknięty w limicie czasu.

Jeśli zdarzenia zachowanie występujące w określonym czasie, okno sesji będzie przechowywać rozszerzanie aż do osiągnięcia maksymalnego czasu trwania. Maksymalny czas trwania interwałów sprawdzanie, czy są ustawiane jako taki sam rozmiar jak określony maksymalny czas trwania. Na przykład, jeśli maksymalny czas trwania wynosi 10, a następnie kontroli, gdy okno przekracza maksymalny czas trwania będzie wystąpić w t = 0, 10, 20, 30, itp.

Po klucz partycji, zdarzenia są zgrupowane za pomocą klucza i okna sesji jest stosowane do każdej grupy niezależnie. Ten podział na partycje jest przydatne w przypadku przypadkach, gdy muszą innej sesji systemu windows dla różnych użytkowników lub urządzeń.


## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

