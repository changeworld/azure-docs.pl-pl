---
title: Nieprawidłowy wzrost liczby wyjątków — usługa Azure Application Insights
description: Monitoruj wyjątki aplikacji z inteligentnym wykrywaniem w usłudze Azure Application Insights dla nietypowych wzorców w woluminie wyjątku.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: a08fae4774a8afb9959f55ea3196cd1a45c33439
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671770"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Nieprawidłowy wzrost liczby wyjątków (podgląd)

Usługa Application Insights automatycznie analizuje wyjątki zgłaszane w aplikacji i może ostrzegać o nietypowych wzorcach w danych telemetrycznych wyjątków.

Ta funkcja nie wymaga specjalnej konfiguracji, innej niż [konfigurowanie raportowania wyjątków](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) dla aplikacji. Jest aktywny, gdy aplikacja generuje wystarczającą liczbę danych telemetrycznych wyjątków.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kiedy otrzymam tego typu powiadomienia o wykrywaniu z inteligentnego wykrywania?
Tego typu powiadomienia mogą zostać otrzymane, jeśli aplikacja wykazuje nieprawidłowy wzrost liczby wyjątków określonego typu w ciągu dnia w porównaniu z linią bazową obliczoną w ciągu ostatnich siedmiu dni.
Algorytmy uczenia maszynowego są używane do wykrywania wzrostu liczby wyjątków, biorąc pod uwagę naturalny wzrost użycia aplikacji.

## <a name="does-my-app-definitely-have-a-problem"></a>Czy moja aplikacja na pewno ma problem?
Nie, powiadomienie nie oznacza, że aplikacja na pewno ma problem. Chociaż nadmierna liczba wyjątków zwykle wskazuje na problem z aplikacją, wyjątki te mogą być łagodne i obsługiwane poprawnie przez aplikację.

## <a name="how-do-i-fix-it"></a>Jak go naprawić?
Powiadomienia zawierają informacje diagnostyczne do obsługi w procesie diagnostyki:
1. **Segregowanie.** Powiadomienie pokazuje, ilu użytkowników lub ile żądań dotyczy. Może to pomóc w przypisaniu priorytetu problemu.
2. **Zakres.** Czy problem dotyczy całego ruchu, czy tylko niektóre działania? Informacje te można uzyskać z powiadomienia.
3. **Zdiagnozować.** Wykrywanie zawiera informacje o metodzie, z której został zgłoszony wyjątek, a także typ wyjątku. Można również użyć powiązanych elementów i raportów łączących się z informacjami pomocniczymi, aby pomóc w dalszym diagnozowaniu problemu.
