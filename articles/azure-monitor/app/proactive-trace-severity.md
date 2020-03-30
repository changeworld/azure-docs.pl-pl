---
title: Spadek współczynnika ważności śledzenia — usługa Azure Application Insights
description: Monitoruj ślady aplikacji za pomocą usługi Azure Application Insights pod kątem nietypowych wzorców w telemetrii śledzenia za pomocą inteligentnego wykrywania.
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 30bdd30ac9c49bb79a3c48bae8149ec761756dd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671685"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Degradacja współczynnika ważności śledzenia (podgląd)

Ślady są szeroko stosowane w aplikacjach, ponieważ pomagają opowiedzieć historię tego, co dzieje się za kulisami. Gdy coś pójdzie nie tak, ślady zapewniają kluczowy wgląd w sekwencję zdarzeń prowadzących do niepożądanego stanu. Podczas gdy ślady są na ogół nieustrukturyzowane, jest jedna rzecz, której konkretnie można się z nich nauczyć - ich poziom nasilenia. W stanie stacjonarnym aplikacji oczekujemy, że stosunek między "dobrymi" śladami *(Info* i *Verbose)* i "złymi" śladami *(Ostrzeżenie,* *Błąd*i *Krytyczny)* pozostanie stabilny. Założenie jest takie, że "złe" ślady mogą zdarzać się regularnie do pewnego stopnia z wielu powodów (na przykład przejściowe problemy z siecią). Ale kiedy prawdziwy problem zaczyna rosnąć, zwykle objawia się wzrostem względnej proporcji "złych" śladów vs "dobrych" śladów. Inteligentne wykrywanie usługi Application Insights automatycznie analizuje ślady zarejestrowane przez aplikację i może ostrzegać o nietypowych wzorcach w ważności danych telemetrycznych śledzenia.

Ta funkcja nie wymaga specjalnej konfiguracji, innej niż konfigurowanie rejestrowania śledzenia dla aplikacji (zobacz jak skonfigurować odbiornik dziennika śledzenia dla [platformy .NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) lub [Java).](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs) Jest aktywny, gdy aplikacja generuje wystarczającą liczbę danych telemetrycznych wyjątków.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kiedy otrzymam tego typu powiadomienia o wykrywaniu z inteligentnego wykrywania?
Tego typu powiadomienia mogą zostać otrzymane, jeśli stosunek między "dobrymi" śladami (śladami zarejestrowanymi z poziomem *informacji* lub *szczegółowymi)* i "złymi" śladami (ślady zarejestrowane z poziomem *ostrzeżenia,* *błędu*lub *fatalnego)* jest rozkładający się w określonym dniu, w porównaniu do planu bazowego obliczonego w ciągu ostatnich siedmiu dni.

## <a name="does-my-app-definitely-have-a-problem"></a>Czy moja aplikacja na pewno ma problem?
Nie, powiadomienie nie oznacza, że aplikacja na pewno ma problem. Chociaż spadek współczynnika między "dobre" i "złe" ślady może wskazywać na problem z aplikacją, ta zmiana współczynnika może być łagodne. Na przykład wzrost może być ze względu na nowy przepływ w aplikacji emitujących więcej "złe" ślady niż istniejące przepływy).

## <a name="how-do-i-fix-it"></a>Jak go naprawić?
Powiadomienia zawierają informacje diagnostyczne do obsługi w procesie diagnostyki:
1. **Segregowanie.** Powiadomienie pokazuje, ile operacji dotyczy. Może to pomóc w przypisaniu priorytetu problemu.
2. **Zakres.** Czy problem dotyczy całego ruchu, czy tylko niektóre działania? Informacje te można uzyskać z powiadomienia.
3. **Zdiagnozować.** Można użyć powiązanych elementów i raportów łączących się z informacjami pomocniczymi, aby pomóc w dalszym diagnozowaniu problemu.


