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
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 9986f57b05032c1e12769d59781e8b7aca443abb
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298995"
---
# <a name="configuring-retention-in-time-series-insights"></a>Konfigurowanie przechowywania w Time Series Insights

W tym artykule opisano sposób konfigurowania zachowania **czasu przechowywania danych** i **limitu magazynowania** w Azure Time Series Insights.

## <a name="summary"></a>Podsumowanie

Każde środowisko Azure Time Series Insights ma ustawienie służące do konfigurowania **czasu przechowywania danych**. Wartość obejmuje od 1 do 400 dni. Dane są usuwane w zależności od pojemności magazynu środowiska lub czasu trwania przechowywania (1-400), zależnie od tego, co nastąpi wcześniej.

Każde środowisko TSI ma dodatkowe zachowanie podczas **przekroczenia limitu magazynowania**. To ustawienie steruje zachowaniem ruchu przychodzącego i przeczyszczania, gdy osiągnięto maksymalną pojemność środowiska. Istnieją dwa zachowania do wyboru:

- **Przeczyść stare dane** (ustawienie domyślne)
- **Wstrzymaj ruch przychodzący**

Aby uzyskać szczegółowe informacje dotyczące lepszego zrozumienia tych ustawień, przejrzyj [Informacje o przechowywaniu w Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurowanie przechowywania danych

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

1. Znajdź istniejące środowisko Time Series Insights. Wybierz pozycję **wszystkie zasoby** w menu po lewej stronie Azure Portal. Wybierz środowisko usługi Time Series Insights.

1. W obszarze **Ustawienia** wybierz pozycję **Konfiguruj**.

    [@no__t 1Select ustawienia, a następnie skonfiguruj](media/data-retention/1-configure-data-retention.png)](media/data-retention/1-configure-data-retention.png#lightbox)

1. Wybierz **czas przechowywania danych (w dniach)** , aby skonfigurować przechowywanie przy użyciu suwaka lub wpisz liczbę w polu tekstowym.

1. Zanotuj ustawienie **wydajności** , ponieważ ta konfiguracja wpływa na maksymalną ilość zdarzeń danych i łączną pojemność magazynu do przechowywania danych.

1. Przełącz ustawienie **zachowania Przekrocz limit magazynu** . Wybierz opcję **Przeczyść stare dane** lub **Wstrzymaj** zachowanie transferu danych przychodzących.

    [@no__t — Zachowaj i Zapisz 1Data.](media/data-retention/2-accept-and-save.png)](media/data-retention/2-accept-and-save.png#lightbox)

1. Zaakceptuj pole wyboru informujące o tym, że dokumentacja została sprawdzona, i poznanie potencjalnych zagrożeń związanych z utratą danych. Wybierz pozycję **Zapisz** , aby skonfigurować zmiany.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zapoznaj się [z tematem przechowywanie w Time Series Insights](time-series-insights-concepts-retention.md).