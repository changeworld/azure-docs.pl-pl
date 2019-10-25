---
title: Inteligentne wykrywanie — nietypowy wzrost w woluminie wyjątku, na platformie Azure Application Insights | Microsoft Docs
description: Monitoruj wyjątki aplikacji za pomocą usługi Azure Application Insights dla nietypowych wzorców w woluminie wyjątku.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/08/2017
ms.openlocfilehash: fa7669b78caabb95b08200e83ed18ea982ce9ac9
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820605"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Nietypowy wzrost w woluminie wyjątku (wersja zapoznawcza)

Application Insights automatycznie analizuje wyjątki zgłoszone w aplikacji i może ostrzec o nietypowych wzorcach w telemetrii wyjątku.

Ta funkcja nie wymaga żadnych specjalnych ustawień, innych niż [Konfigurowanie raportowania wyjątków](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) dla aplikacji. Jest ona aktywna, gdy aplikacja generuje wystarczającą ilość danych telemetrycznych wyjątku.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kiedy mam ten typ powiadomienia inteligentnego wykrywania?
Ten typ powiadomienia może zostać wyświetlony, jeśli w aplikacji występuje nietypowy wzrost liczby wyjątków określonego typu w ciągu dnia, w porównaniu z linią bazową obliczoną w ciągu ostatnich siedmiu dni.
Algorytmy uczenia maszynowego są używane do wykrywania wzrostu liczby wyjątków, przy jednoczesnym uwzględnieniu naturalnego wzrostu użycia aplikacji.

## <a name="does-my-app-definitely-have-a-problem"></a>Czy moja aplikacja ma w nieskończoność problem?
Nie, powiadomienie nie oznacza, że w aplikacji występuje problem. Chociaż nadmierna liczba wyjątków zwykle wskazuje na problem z aplikacją, te wyjątki mogą być niegroźne i prawidłowo obsługiwane przez aplikację.

## <a name="how-do-i-fix-it"></a>Jak mogę rozwiązać ten problem?
Powiadomienia obejmują informacje diagnostyczne do obsługi procesu diagnostyki:
1. **Klasyfikacja.** Powiadomienie przedstawia liczbę użytkowników lub liczbę żądań, których dotyczy. Może to pomóc w przypisaniu priorytetu do problemu.
2. **Scope.** Czy problem wpływa na cały ruch, czy tylko niektóre operacje? Te informacje można uzyskać na podstawie powiadomienia.
3. **Sprawdź.** Wykrywanie zawiera informacje o metodzie, z której został zgłoszony wyjątek, a także typ wyjątku. Możesz również użyć powiązanych elementów i raportów łączących się z informacjami pomocniczymi, aby pomóc w dalszej analizie problemu.