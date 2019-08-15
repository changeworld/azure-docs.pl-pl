---
title: Jak skonfigurować przechowywanie w środowisku Azure Time Series Insights | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania przechowywania w środowisku Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: e5cc1489af1dce3a9a57b96a3240c63e0395c33a
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947229"
---
# <a name="configuring-retention-in-time-series-insights"></a>Konfigurowanie przechowywania w Time Series Insights

W tym artykule opisano sposób konfigurowania zachowania **czasu przechowywania danych** i **limitu magazynowania** w Azure Time Series Insights.

## <a name="summary"></a>Podsumowanie

Każde środowisko Time Series Insights (TSI) zawiera ustawienie służące do konfigurowania **czasu przechowywania danych**. Wartość obejmuje od 1 do 400 dni. Dane są usuwane w zależności od pojemności magazynu środowiska lub czasu trwania przechowywania (1-400), zależnie od tego, co nastąpi wcześniej.

Każde środowisko TSI ma dodatkowe zachowanie podczas **przekroczenia limitu magazynowania**. To ustawienie steruje zachowaniem ruchu przychodzącego i przeczyszczania, gdy osiągnięto maksymalną pojemność środowiska. Istnieją dwa zachowania do wyboru:

- **Przeczyść stare dane** wartooć
- **Wstrzymaj ruch przychodzący**

Aby uzyskać szczegółowe informacje dotyczące lepszego zrozumienia tych ustawień, przejrzyj [Informacje o przechowywaniu w Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurowanie przechowywania danych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Znajdź istniejącego środowiska usługi Time Series Insights. Wybierz pozycję **wszystkie zasoby** w menu po lewej stronie Azure Portal. Wybierz środowisko usługi Time Series Insights.

1. W obszarze **Ustawienia** wybierz pozycję **Konfiguruj**.

1. Wybierz **czas przechowywania danych** , aby skonfigurować przechowywanie przy użyciu suwaka lub wpisz liczbę w polu tekstowym.

1. Zanotuj ustawienie **wydajności** , ponieważ ta konfiguracja wpływa na maksymalną ilość zdarzeń danych i łączną pojemność magazynu do przechowywania danych.

1. Przełącz ustawienie **zachowania Przekrocz limit magazynu** . Wybierz opcję **Przeczyść stare dane** lub **Wstrzymaj** zachowanie transferu danych przychodzących.

1. Wybierz pozycję **Zapisz** , aby skonfigurować zmiany.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zapoznaj się [z tematem przechowywanie w Time Series Insights](time-series-insights-concepts-retention.md).