---
title: Inteligentne wykrywanie — Potencjalny przeciek pamięci, wykrytych przez usługę Azure Application Insights | Dokumentacja firmy Microsoft
description: Monitoruj aplikacje za pomocą usługi Azure Application Insights dla potencjalnych przeciekach pamięci.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: e430b1e976ac26f7320b28d50dd39923066cfa41
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60306347"
---
# <a name="memory-leak-detection-preview"></a>Wykrywania przecieków pamięci (wersja zapoznawcza)

Usługa Application Insights automatycznie analizuje zmniejszenie zużycia pamięci każdego procesu w aplikacji i może zostać wyświetlone ostrzeżenie o potencjalnych przeciekach pamięci lub użycie większej ilości pamięci.

Ta funkcja wymaga nie specjalne ustawienia innego niż [Konfigurowanie liczników wydajności](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) dla aplikacji. Może to być aktywny, gdy aplikacja generuje wystarczającej ilości pamięci telemetrii liczników wydajności (na przykład Bajty prywatne).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Gdy otrzyma ten typ powiadomienia wykrywania inteligentnego
Typowe powiadomienia będą zgodne z spójne wzrost użycia pamięci przez dłuższy czas, w jeden lub więcej procesów i/lub co najmniej jednej maszyny, które są częścią Twojej aplikacji. Algorytmów uczenia maszynowego są używane do wykrywania użycia większej ilości pamięci, który pasuje do wzorca przeciek pamięci.

## <a name="does-my-app-really-have-a-problem"></a>Czy Moja aplikacja ma naprawdę problem?
Nie, powiadomienia nie oznacza, że Twoja aplikacja ma zdecydowanie problem. Mimo że wzorce przeciek pamięci zazwyczaj wskazują problem aplikacji, te wzorce może być typową dla procesu określonego lub może mieć uzasadnienie biznesowe naturalnych i można zignorować.

## <a name="how-do-i-fix-it"></a>Jak go naprawić?
Powiadomienia obejmują informacje diagnostyczne do obsługi w procesie diagnostyczne analizy:
1. **Klasyfikacja.** Powiadomienie zawiera informację, można zwiększyć ilość pamięci, (w GB) i zakres czasu, w którym wzrosła pamięci. Może to pomóc Ci przypisać priorytet problemu.
2. **Zakres.** Ile maszyn uwidocznione wzorzec przeciek pamięci? Jak wiele wyjątków zostały wyzwolone Potencjalny przeciek pamięci? Te informacje można uzyskać z powiadomienia.
3. **Diagnozowanie.** Wykrywanie zawiera wzorzec przeciek pamięci, przedstawiający zużycie pamięci procesu wraz z upływem czasu. Umożliwia także elementy pokrewne i raporty dodatkowe informacje ułatwiające dalsze łączenie zdiagnozowania problemu.
