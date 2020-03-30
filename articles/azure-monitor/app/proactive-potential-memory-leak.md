---
title: Wykrywanie przecieku pamięci — inteligentne wykrywanie usługi Azure Application Insights
description: Monitoruj aplikacje za pomocą usługi Azure Application Insights pod kątem potencjalnych przecieków pamięci.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 85d138518dfb1313a810657016e9fe3143887b6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671702"
---
# <a name="memory-leak-detection-preview"></a>Wykrywanie przecieków pamięci (podgląd)

Usługa Application Insights automatycznie analizuje zużycie pamięci każdego procesu w aplikacji i może ostrzegać o potencjalnych przeciekach pamięci lub zwiększonym zużyciu pamięci.

Ta funkcja nie wymaga specjalnej konfiguracji, poza [konfigurowaniem liczników wydajności](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) aplikacji. Jest aktywny, gdy aplikacja generuje wystarczającą ilość wydajności pamięci liczniki telemetrii (na przykład bajty prywatne).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kiedy otrzymam tego typu powiadomienia o wykrywaniu z inteligentnego wykrywania?
Typowe powiadomienie będzie śledzić stały wzrost zużycia pamięci przez długi okres czasu, w jednym lub więcej procesów i/lub jeden lub więcej maszyn, które są częścią aplikacji. Algorytmy uczenia maszynowego są używane do wykrywania zwiększonego zużycia pamięci, który pasuje do wzorca przecieku pamięci.

## <a name="does-my-app-really-have-a-problem"></a>Czy moja aplikacja naprawdę ma problem?
Nie, powiadomienie nie oznacza, że aplikacja na pewno ma problem. Mimo że wzorce przeciek pamięci zwykle wskazują na problem z aplikacją, te wzorce mogą być typowe dla określonego procesu lub mogą mieć naturalne uzasadnienie biznesowe i mogą być ignorowane.

## <a name="how-do-i-fix-it"></a>Jak go naprawić?
Powiadomienia obejmują informacje diagnostyczne do obsługi w procesie analizy diagnostycznej:
1. **Segregowanie.** Powiadomienie pokazuje ilość zwiększenia pamięci (w GB) i zakres czasu, w którym pamięć wzrosła. Może to pomóc w przypisaniu priorytetu problemu.
2. **Zakres.** Ile maszyn wystawiło wzór przecieku pamięci? Ile wyjątków zostało wyzwolonych podczas potencjalnego wycieku pamięci? Informacje te można uzyskać z powiadomienia.
3. **Zdiagnozować.** Wykrywanie zawiera wzorzec przecieku pamięci, pokazując zużycie pamięci procesu w czasie. Można również użyć powiązanych elementów i raportów łączących się z informacjami pomocniczymi, aby pomóc w dalszym diagnozowaniu problemu.
