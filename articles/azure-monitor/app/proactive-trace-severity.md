---
title: Inteligentne wykrywanie — obniżenie wydajności współczynnik ważność śledzenia, w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Monitoruj śladów aplikacji za pomocą usługi Azure Application Insights nietypowe wzorce w danych telemetrycznych śledzenia.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: mbullwin
ms.openlocfilehash: 10b909fd5239546047aa4696a1f6a68a703778c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306398"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Obniżenie wydajności stopień ważności śledzenia (wersja zapoznawcza)

Ślady są często stosowane w aplikacji, ponieważ one pomóc, Opowiedz historię co się dzieje w tle. Gdy coś pójdzie źle, ślady wgląd niezwykle istotne w sekwencji zdarzeń, co prowadzi do stanu niepożądane. Ślady są zazwyczaj bez struktury, istnieje jeden element, który może być rozpoznawane konkretnie z nich — ich poziom ważności. W stanie stabilności aplikacji, czy oczekujemy, że stosunek ślady "dobre" (*informacje* i *pełne*) i "złe" ślady (*ostrzeżenie*, *błąd*, i *krytyczny*) pozostaną niezmienione. Zakłada się, że ślady "złe" może się zdarzyć, regularnie do pewnego stopnia ze względu na dowolną liczbę powodów (na przykład w przejściowe problemy z siecią problemy). Jednak po rozpoczęciu rosnący rzeczywisty problem zwykle manifesty jako wzrost udział ślady "dobre" a "złe" ślady. Application Insights, Inteligentne wykrywanie automatycznie analizuje ślady zarejestrowane przez aplikację i może zostać wyświetlone ostrzeżenie dotyczące niezwykłych wzorców ważność telemetrii śledzenia.

Ta funkcja wymaga nie specjalne ustawienia innego niż Konfigurowanie rejestrowania śledzenia dla aplikacji (zobacz, jak Konfigurowanie odbiornika dziennika śledzenia [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) lub [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). Może to być aktywny, gdy aplikacja generuje wystarczającej ilości danych telemetrycznych wyjątku.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Gdy otrzyma ten typ powiadomienia wykrywania inteligentnego
Może zostać wyświetlony ten typ powiadomienia, gdy stosunek ślady "dobre" (ślady zarejestrowane z poziomem *informacje* lub *pełne*) i ślady "złe" (ślady zarejestrowane z poziomem *ostrzeżenie*, *Błąd*, lub *błąd krytyczny*) jest zmniejszenie w określonym dniu, w porównaniu do linii bazowej obliczane w ciągu ostatnich siedmiu dni.

## <a name="does-my-app-definitely-have-a-problem"></a>Czy Moja aplikacja ma zdecydowanie problem?
Nie, powiadomienia nie oznacza, że Twoja aplikacja ma zdecydowanie problem. Mimo że pogorszenie stosunek "dobre" traces "złe" może oznaczać problemu z aplikacją, ta zmiana w stosunku może być niegroźne. Na przykład wzrostem może być ze względu na nowy przepływ w aplikacji emitowania więcej ślady "złe" niż do istniejących przepływów).

## <a name="how-do-i-fix-it"></a>Jak go naprawić?
Powiadomienia obejmują informacje diagnostyczne do obsługi w procesie diagnostyki:
1. **Klasyfikacja.** Powiadomienie zawiera informację, ile operacji dotyczy problem. Może to pomóc Ci przypisać priorytet problemu.
2. **Zakres.** Ten problem wpływa na cały ruch lub tylko niektórych operacji? Te informacje można uzyskać z powiadomienia.
3. **Diagnozowanie.** Możesz użyć elementy pokrewne i raporty dodatkowe informacje ułatwiające dalsze łączenie zdiagnozowania problemu.


