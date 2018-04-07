---
title: Wprowadzenie do usługi Azure Stream Analytics funkcje okien
description: W tym artykule opisano trzy okien funkcje (wirowania, skaczące, przedłużanie) są używane w zadania usługi analiza strumienia Azure.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: c6f5dbe49cb60e3c7b2bc6562acf2d7fd79096ec
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="introduction-to-stream-analytics-window-functions"></a>Wprowadzenie do funkcji okna usługi analiza strumienia
W czasie rzeczywistym wiele przesyłania strumieniowego scenariusze należy wykonywać operacje tylko na dane zawarte w danych czasowych systemu windows. Macierzystą obsługę funkcji okien jest kluczowym elementem usługi Azure Stream Analytics, który przenosi wskazówka na produktywność deweloperów w tworzeniu zadania przetwarzania złożonych strumienia. Analiza strumienia umożliwia deweloperom korzystanie [ **wirowania**](https://msdn.microsoft.com/library/dn835055.aspx), [ **Hopping** ](https://msdn.microsoft.com/library/dn835041.aspx) i [ **ruchomej** ](https://msdn.microsoft.com/library/dn835051.aspx) systemu windows do wykonywania operacji danych czasowych na przesyłanie strumieniowe danych. Warto zauważyć, że wszystkie [okna](https://msdn.microsoft.com/library/dn835019.aspx) wyników w danych wyjściowych operacji **zakończenia** okna. Dane wyjściowe okna będą pojedyncze zdarzenie oparte na funkcji agregującej używane. Zdarzenie będzie mieć sygnaturę czasową koniec okna i wszystkie funkcje okna są zdefiniowane o stałej długości. Na koniec należy pamiętać, że wszystkie funkcje okna powinny być używane w jest [ **GROUP BY** ](https://msdn.microsoft.com/library/dn835023.aspx) klauzuli.

![Pojęcia dotyczące funkcji okno Analiza strumienia](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Okno wirowania
Wirowania okna, które funkcje są używane do segmentu strumienia danych do czasu odrębnych segmentach i wykonywania funkcji, takich jak w poniższym przykładzie. Klucza wyróżniającymi okna wirowania są, że powtarzają, nie nakładają się i zdarzenia nie może należeć do więcej niż jedno okno wirowania.

![Funkcje okna usługi analiza strumienia wirowania wprowadzenie](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Okno przeskoku
Przeskoku okna funkcji przeskok do przodu w czasie w ustalonym okresie. Może to być łatwo traktować ich jako wirowania systemu windows, które mogą nakładać się na, więc zdarzeń mogą należeć do więcej niż jeden zestaw wyników okna Hopping. Aby utworzyć okno Hopping taki sam jak wirowania okna jeden po prostu określić rozmiar przeskoku na taki sam rozmiar okna. 

![Okno Analiza strumienia funkcje przeskoku wprowadzenie](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Na metodzie przesuwanego okna
Funkcje przesuwanego okna, w odróżnieniu od wirowania lub skaczące systemu windows, utworzenie danych wyjściowych **tylko** po wystąpieniu zdarzenia. Co okno będzie zawierać co najmniej jedno zdarzenie i okna stale przenosi do przodu € (epsilon). Podobnie jak w przypadku Windows skaczące zdarzeń mogą należeć do więcej niż jedno okno przedłużanie.

![Przedłużanie wprowadzenie funkcje okno Analiza strumienia](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Uzyskiwanie pomocy z funkcji okna
Aby uzyskać dalszą pomoc, skorzystaj z naszego [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

