---
title: Sposób konfigurowania przechowywania w środowisku Azure czas serii Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania przechowywania w środowisku Azure czas serii Insights.
ms.service: time-series-insights
services: time-series-insights
author: anshan
ms.author: anshan
manager: kfile
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 9a236ba500af36e8fb1c8c089389ffd74b9d2abb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653923"
---
# <a name="configuring-retention-in-time-series-insights"></a>Konfigurowanie przechowywania w czasie serii Insights
W tym artykule opisano sposób konfigurowania **czas przechowywania danych** i **zachowanie przekroczony limit magazynu** w usłudze Azure czas serii Insights.

Każde środowisko czasu serii Insights (TSI) ma ustawienie, aby skonfigurować **czas przechowywania danych**. Wartość jest liczony od 1 do 400 dni. Dane zostaną usunięte, oparte na środowisku pojemności lub przechowywania okresem magazynu (1-400), zależnie od zostanie osiągnięty jako pierwszy.

Każde środowisko TSI ma dodatkowe ustawienie **zachowanie przekroczony limit magazynu**. To ustawienie określa przychodzące i przeczyszczania zachowanie po osiągnięciu maksymalnej pojemności środowisku. Istnieją dwa zachowania do wyboru:
- **Przeczyść stare dane** (ustawienie domyślne)  
- **Transfer danych przychodzących Wstrzymaj**

Aby uzyskać szczegółowe informacje lepiej zrozumieć te ustawienia, przejrzyj [opis czas przechowywania danych w czasie serii Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurowanie przechowywania danych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Znajdź istniejące środowisko Insights serii czasu. Wybierz **wszystkie zasoby** w menu po lewej stronie portalu Azure. Wybierz środowisko usługi Time Series Insights.

3. W obszarze **ustawienia** nagłówek, wybierz **Konfiguruj**.

4. Wybierz **czas przechowywania danych** konfigurowania przechowywania za pomocą suwaka, lub wpisz liczbę w polu tekstowym.

5. Uwaga **pojemności** ustawienie, ponieważ ta konfiguracja ma wpływ na maksymalną ilość danych dotyczących zdarzeń i pojemność pamięci masowej do przechowywania danych. 

6. Przełącz **zachowanie przekroczony limit magazynu** ustawienie. Wybierz **przeczyścić stare dane** lub **wstrzymać wejściowych** zachowanie.

7. Wybierz **zapisać** skonfigurować zmiany.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, przejrzyj [opis czas przechowywania danych w czasie serii Insights](time-series-insights-concepts-retention.md).
