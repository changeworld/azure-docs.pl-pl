---
title: Konfigurowanie agenta źródeł danych w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Źródła danych definiują dane dziennika, że usługa Azure Monitor zbiera z agentów i inne połączone źródła.  W tym artykule opisano pojęcia jak Azure Monitor korzysta ze źródeł danych, zawiera szczegółowe informacje o sposobach ich konfigurowania i zawiera podsumowanie informacji o dostępnych źródeł danych.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: d7d4aa89c4dcf2ac9cc0c393e0481cae1f3aeaf2
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58847017"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Agent źródeł danych w usłudze Azure Monitor
Dane usługi Azure Monitor zbiera dane z agentów jest definiowany przez źródeł danych, które można skonfigurować.  Dane z agentów są przechowywane jako [dane dziennika](data-platform-logs.md) przy użyciu zestawu rekordów.  Każde źródło danych tworzy rekordy określonego typu, za pomocą poszczególnych typów posiadanie swój własny zestaw właściwości.

![Zbieranie danych dziennika](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Podsumowanie źródeł danych
Poniższa tabela zawiera listę źródeł danych agenta, które są obecnie dostępne w usłudze Azure Monitor.  Każda ma link do artykułu oddzielne podanie szczegółów dla tego źródła danych.   Zawiera także informacje o ich metody i częstotliwość zbierania. 


| Źródło danych | Platforma | Agent monitorowania firmy Microsoft | Agent programu Operations Manager | Azure Storage | Wymagane programu Operations Manager? | Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Niestandardowe dzienniki](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | Po dostarczeniu |
| [Niestandardowe dzienniki](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | Po dostarczeniu |
| [Dzienniki usług IIS](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |zależy od ustawienia Przerzucanie pliku dziennika |
| [Liczniki wydajności](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |zgodnie z harmonogramem, co najmniej 10 sekund |
| [Liczniki wydajności](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |zgodnie z harmonogramem, co najmniej 10 sekund |
| [Dziennik systemu](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |z usługi Azure storage: 10 minut; od agenta: po przybyciu |
| [Dzienniki zdarzeń Windows](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | Po dostarczeniu |


## <a name="configuring-data-sources"></a>Konfigurowanie źródeł danych
Konfigurowanie źródła danych z **danych** menu **Zaawansowane ustawienia** dla obszaru roboczego.  Dowolna konfiguracja jest dostarczana do wszystkich połączonych źródeł, w obszarze roboczym.  Obecnie nie możesz wykluczać wszelkich agentów z tej konfiguracji.

![Konfiguruj zdarzenia Windows](media/agent-data-sources/configure-events.png)

1. W witrynie Azure portal wybierz **obszarów roboczych usługi Log Analytics** > obszar roboczy > **Zaawansowane ustawienia**.
2. Wybierz **danych**.
3. Kliknij źródło danych, które chcesz skonfigurować.
4. Kliknij link do dokumentacji dla każdego źródła danych w powyższej tabeli, aby uzyskać szczegółowe informacje na ich konfiguracji.


## <a name="data-collection"></a>Zbieranie danych
Konfiguracje źródeł danych są dostarczane do agentów, które są podłączone bezpośrednio do usługi Azure Monitor w ciągu kilku minut.  Określone dane jest zbierane z programu agent i dostarczane bezpośrednio do usługi Azure Monitor w odstępach specyficzne dla każdego źródła danych.  Zobacz dokumentację dla każdego źródła danych dla tych szczegółowych informacji.

Agenci programu System Center Operations Manager w podłączonej grupy zarządzania konfiguracje źródeł danych są przetłumaczone na pakiety administracyjne i dostarczane do grupy zarządzania co 5 minut, domyślnie.  Agent pobiera pakiet administracyjny, jak każdy inny i zbiera określone dane. W zależności od źródła danych dane będą albo wysłane do serwera zarządzania, która przekazuje dane do usługi Azure Monitor lub agent będzie wysyłać dane do usługi Azure Monitor bez przechodzenia przez serwer zarządzania. Zobacz [szczegóły zbierania danych monitorowania rozwiązań na platformie Azure](../insights/solutions-inventory.md) Aby uzyskać szczegółowe informacje.  Możesz przeczytać o szczegółach połączenie programu Operations Manager i usługi Azure Monitor i modyfikowanie częstotliwości tej konfiguracji jest dostarczana w [konfigurowania integracji z programem System Center Operations Manager](om-agents.md).

Jeśli agent jest w stanie połączyć się z usługi Azure Monitor lub Operations Manager, będzie zbierać dane, które będzie dostarczać, jeśli go nawiązuje połączenie.  Dane mogą zostać utracone, jeśli ilość danych osiągnie maksymalny rozmiar pamięci podręcznej klienta lub jeśli agent nie jest w stanie nawiązać połączenie w ciągu 24 godzin.

## <a name="log-records"></a>Rekordy dziennika
Wszystkie dane dzienników zbieranych przez usługi Azure Monitor jest przechowywane w obszarze roboczym jako rekordy.  Rekordów zbieranych przez różne źródła danych będą mieć własne zestawy właściwości i zostać zidentyfikowane na podstawie ich **typu** właściwości.  Zobacz dokumentację dla każdego źródła danych i rozwiązań, aby uzyskać szczegółowe informacje dla każdego typu rekordu.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [rozwiązania do monitorowania](../insights/solutions.md) , dodawanie funkcji do usługi Azure Monitor i również zbierać dane do obszaru roboczego.
* Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md) analizować dane zebrane ze źródeł danych i rozwiązania do monitorowania.  
* Konfigurowanie [alerty](alerts-overview.md) do aktywnego powiadamiania użytkownika kluczowych danych zbieranych ze źródeł danych i rozwiązania do monitorowania.