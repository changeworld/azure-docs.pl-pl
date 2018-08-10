---
title: Jak skonfigurować przechowywania w danym środowisku usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania przechowywania w danym środowisku usługi Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: a87a12e08e78cf8a7f0b4d2af3e8029b233cf02b
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629892"
---
# <a name="configuring-retention-in-time-series-insights"></a>Konfigurowanie przechowywania w usłudze Time Series Insights
W tym artykule opisano sposób konfigurowania **czas przechowywania danych** i **zachowanie przekroczony limit magazynu** w usłudze Azure Time Series Insights.

Każde środowisko usługi Time Series Insights (TSI) ma ustawienie, aby skonfigurować **czas przechowywania danych**. Wartość rozciąga się od 1 do 400 dni. Dane zostaną usunięte, oparte na środowisku pojemności lub przechowywania czas magazynowania (1 – 400), osiągnięta jako pierwsza.

Każde środowisko usługi TSI ma dodatkowe ustawienie **zachowanie przekroczony limit magazynu**. To ustawienie kontroluje zachowanie transferu danych przychodzących i czyszczenie, gdy zostanie osiągnięta maksymalna wydajność środowiska. Istnieją dwa zachowania do wyboru:
- **Usuwanie starych danych** (ustawienie domyślne)  
- **Wstrzymaj transferu danych przychodzących**

Aby uzyskać szczegółowe informacje lepiej zrozumieć te ustawienia, przejrzyj [przechowywania opis usługi Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurowanie przechowywania danych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Znajdź istniejącego środowiska usługi Time Series Insights. Wybierz **wszystkie zasoby** w menu po lewej stronie witryny Azure portal. Wybierz środowisko usługi Time Series Insights.

3. W obszarze **ustawienia** nagłówka, wybierz **Konfiguruj**.

4. Wybierz **czas przechowywania danych** skonfigurowanie okresu przechowywania, przy użyciu suwaka lub wpisz liczbę w polu tekstowym.

5. Uwaga **pojemności** ustawienia, ponieważ ta konfiguracja wpływa negatywnie na maksymalną ilość danych dotyczących zdarzeń i całkowitej pojemności do przechowywania danych. 

6. Przełącz **zachowanie przekroczony limit magazynu** ustawienie. Wybierz **wyczyścić stare dane** lub **wstrzymać ruch przychodzący** zachowanie.

7. Wybierz **Zapisz** skonfigurować zmiany.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, zobacz [przechowywania opis usługi Time Series Insights](time-series-insights-concepts-retention.md).
