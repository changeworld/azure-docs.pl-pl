---
title: Konfigurowanie źródeł danych w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Źródła danych definiują dane, czy usługi Log Analytics zbiera z agentów i inne połączone źródła.  W tym artykule opisano pojęcia, jak usługa Log Analytics korzysta ze źródeł danych, zawiera szczegółowe informacje o sposobach ich konfigurowania i zawiera podsumowanie informacji o różnych źródeł danych dostępne.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/26/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 8dad4c11ac309d959675d525fbeb48fe385cf4a5
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336964"
---
# <a name="data-sources-in-log-analytics"></a>Źródła danych w usłudze Log Analytics
Log Analytics zbiera dane z połączonych źródeł i zapisuje go w obszarze roboczym usługi Log Analytics.  Dane, które są zbierane z każdego jest definiowany przez źródeł danych, które konfigurujesz.  Dane w usłudze Log Analytics są przechowywane jako zestaw rekordów.  Każde źródło danych tworzy rekordy określonego typu, za pomocą poszczególnych typów posiadanie swój własny zestaw właściwości.

![Zaloguj się zbieranie danych analitycznych](./media/agent-data-sources/overview.png)

Źródła danych różnią się od [rozwiązań do zarządzania](../../azure-monitor/insights/solutions.md), który również zbierać dane z połączonych źródeł i tworzenie rekordów w usłudze Log Analytics.  Oprócz zbierania danych, rozwiązania zwykle zawierają wyszukiwań w dziennikach i widoków, aby pomóc w analizie operacji określonej aplikacji lub usługi.


## <a name="summary-of-data-sources"></a>Podsumowanie źródeł danych
Poniższa tabela zawiera listę źródeł danych, które są obecnie dostępne w usłudze Log Analytics.  Każda ma link do artykułu oddzielne podanie szczegółów dla tego źródła danych.   Zawiera także informacje o ich metody i częstotliwość zbierania danych w usłudze Log Analytics.  Identyfikowanie różnych rozwiązaniach dostępnych i zrozumienie wymagań połączenia i przepływu danych dla rozwiązań do zarządzania różnych, można użyć informacji w tym artykule. Objaśnienia dotyczące kolumn, zobacz [szczegóły zbierania danych dla rozwiązań do zarządzania na platformie Azure](../../azure-monitor/insights/solutions-inventory.md).


| Źródło danych | Platforma | Agent monitorowania firmy Microsoft | Agent programu Operations Manager | Azure Storage | Wymagane programu Operations Manager? | Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [Niestandardowe dzienniki](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | Po dostarczeniu |
| [Niestandardowe dzienniki](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | Po dostarczeniu |
| [Dzienniki usług IIS](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |zależy od ustawienia Przerzucanie pliku dziennika |
| [Liczniki wydajności](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |zgodnie z harmonogramem, co najmniej 10 sekund |
| [Liczniki wydajności](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |zgodnie z harmonogramem, co najmniej 10 sekund |
| [Dziennik systemu](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |z usługi Azure storage: 10 minut; od agenta: po przybyciu |
| [Dzienniki zdarzeń Windows](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | Po dostarczeniu |


## <a name="configuring-data-sources"></a>Konfigurowanie źródeł danych
Konfigurowanie źródła danych z **danych** menu w usłudze Log Analytics **Zaawansowane ustawienia**.  Dowolna konfiguracja jest dostarczana do wszystkich połączonych źródeł, w obszarze roboczym.  Obecnie nie możesz wykluczać wszelkich agentów z tej konfiguracji.

![Konfiguruj zdarzenia Windows](./media/agent-data-sources/configure-events.png)

1. W witrynie Azure portal wybierz **usługi Log Analytics** > obszar roboczy > **Zaawansowane ustawienia**.
2. Wybierz **danych**.
3. Kliknij źródło danych, które chcesz skonfigurować.
4. Kliknij link do dokumentacji dla każdego źródła danych w powyższej tabeli, aby uzyskać szczegółowe informacje na ich konfiguracji.


## <a name="data-collection"></a>Zbieranie danych
Konfiguracje źródeł danych są dostarczane do agentów, które są podłączone bezpośrednio do usługi Log Analytics w ciągu kilku minut.  Określone dane jest zbierane z programu agent i dostarczane bezpośrednio do usługi Log Analytics w odstępach specyficzne dla każdego źródła danych.  Zobacz dokumentację dla każdego źródła danych dla tych szczegółowych informacji.

Agenci programu System Center Operations Manager w podłączonej grupy zarządzania konfiguracje źródeł danych są przetłumaczone na pakiety administracyjne i dostarczane do grupy zarządzania co 5 minut, domyślnie.  Agent pobiera pakiet administracyjny, jak każdy inny i zbiera określone dane. W zależności od źródła danych dane będą albo wysłane do serwera zarządzania, która przekazuje dane do usługi Log Analytics lub agent będzie wysyłać dane do usługi Log Analytics bez przechodzenia przez serwer zarządzania. Zobacz [szczegóły zbierania danych dla rozwiązań do zarządzania na platformie Azure](../../azure-monitor/insights/solutions-inventory.md) Aby uzyskać szczegółowe informacje.  Możesz przeczytać o szczegółach połączenie programu Operations Manager i usługi Log Analytics i modyfikowanie częstotliwości tej konfiguracji jest dostarczana w [konfigurowania integracji z programem System Center Operations Manager](../../log-analytics/log-analytics-om-agents.md).

Jeśli agent jest w stanie połączyć się z usługi Log Analytics lub Operations Manager, będzie zbierać dane, które będzie dostarczać, jeśli go nawiązuje połączenie.  Dane mogą zostać utracone, jeśli ilość danych osiągnie maksymalny rozmiar pamięci podręcznej klienta lub jeśli agent nie jest w stanie nawiązać połączenie w ciągu 24 godzin.

## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics
Wszystkie dane zebrane przez usługę Log Analytics jest przechowywane w obszarze roboczym jako rekordy.  Rekordów zbieranych przez różne źródła danych będą mieć własne zestawy właściwości i zostać zidentyfikowane na podstawie ich **typu** właściwości.  Zobacz dokumentację dla każdego źródła danych i rozwiązań, aby uzyskać szczegółowe informacje dla każdego typu rekordu.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [rozwiązania](../../azure-monitor/insights/solutions.md) , dodawanie funkcji do usługi Log Analytics i również zbierać dane do obszaru roboczego.
* Dowiedz się więcej o [dziennikach](../../log-analytics/log-analytics-queries.md) analizować dane zbierane z innych źródeł danych i rozwiązań.  
* Konfigurowanie [alerty](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) celu proaktywnego powiadamiania o krytyczne dane zbierane z innych źródeł danych i rozwiązań.
