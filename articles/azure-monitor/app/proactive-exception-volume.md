---
title: Inteligentne wykrywanie - nietypowy wzrost woluminu wyjątków, w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Monitorowanie wyjątków aplikacji za pomocą usługi Azure Application Insights nietypowe wzorce w wzrost liczby wyjątków.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: mbullwin
ms.openlocfilehash: a6e7e8e01ccb623a3ff340c318c9c238c919cb38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61298588"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Nietypowy wzrost liczby wyjątków (wersja zapoznawcza)

Usługa Application Insights automatycznie analizuje wyjątków zgłoszonych w aplikacji i może zostać wyświetlone ostrzeżenie dotyczące niezwykłych wzorców w danych telemetrycznych wyjątku.

Ta funkcja wymaga nie specjalne ustawienia innego niż [konfigurowanie raportowania wyjątek](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) dla aplikacji. Może to być aktywny, gdy aplikacja generuje wystarczającej ilości danych telemetrycznych wyjątku.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Gdy otrzyma ten typ powiadomienia wykrywania inteligentnego
Jeśli aplikacja jest wykazujących nietypowy wzrost liczby wyjątków o typie określonym w ciągu dnia, w porównaniu do linii bazowej obliczane w ciągu ostatnich siedmiu dni, możesz otrzymać ten typ powiadomienia.
Algorytmów uczenia maszynowego, są używane do wykrywania wzrost liczby wyjątków, biorąc pod uwagę przyrost naturalny w użycie aplikacji.

## <a name="does-my-app-definitely-have-a-problem"></a>Czy Moja aplikacja ma zdecydowanie problem?
Nie, powiadomienia nie oznacza, że Twoja aplikacja ma zdecydowanie problem. Mimo że nadmierną liczbę wyjątków zwykle wskazuje problemów aplikacji, tych wyjątków może być nieszkodliwe i obsługiwane poprawnie przez aplikację.

## <a name="how-do-i-fix-it"></a>Jak go naprawić?
Powiadomienia obejmują informacje diagnostyczne do obsługi w procesie diagnostyki:
1. **Klasyfikacja.** Powiadomienie zawiera informację, ilu użytkowników lub wpływają na liczbę żądań. Może to pomóc Ci przypisać priorytet problemu.
2. **Zakres.** Ten problem wpływa na cały ruch lub tylko niektórych operacji? Te informacje można uzyskać z powiadomienia.
3. **Diagnozowanie.** Wykrywanie zawiera informacje o metodzie, w którym wystąpił wyjątek, a także typ wyjątku. Umożliwia także elementy pokrewne i raporty dodatkowe informacje ułatwiające dalsze łączenie zdiagnozowania problemu.