---
title: Konfigurowanie źródeł danych agenta w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Źródła danych definiują dane dziennika, które usługa Azure Monitor zbiera od agentów i innych połączonych źródeł.  W tym artykule opisano koncepcję sposobu korzystania ze źródeł danych usługi Azure Monitor, wyjaśniono szczegółowe informacje na temat konfigurowania ich i przedstawiono podsumowanie różnych dostępnych źródeł danych.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aec3fe2386ce916c556f6da295a8554fff140259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249102"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Źródła danych agenta w usłudze Azure Monitor
Dane zbierane przez usługę Azure Monitor od agentów są definiowane przez źródła danych, które konfigurujesz.  Dane z agentów są przechowywane jako [dane dziennika](data-platform-logs.md) z zestawem rekordów.  Każde źródło danych tworzy rekordy określonego typu, przy czym każdy typ ma własny zestaw właściwości.

![Zbieranie danych dziennika](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Podsumowanie źródeł danych
W poniższej tabeli wymieniono źródła danych agenta, które są obecnie dostępne w usłudze Azure Monitor.  Każdy ma łącze do oddzielnego artykułu zawierającego szczegółowe informacje dla tego źródła danych.   Zawiera również informacje na temat ich metody i częstotliwości zbierania. 


| Źródło danych | Platforma | Agent analizy dzienników | Agent programu Operations Manager | Azure Storage | Wymagany jest program Operations Manager? | Dane agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Niestandardowe dzienniki](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | po przyjeździe |
| [Niestandardowe dzienniki](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | po przyjeździe |
| [Dzienniki usług IIS](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |zależy od ustawienia Narzucie pliku dziennika |
| [Liczniki wydajności](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |zgodnie z harmonogramem, minimum 10 sekund |
| [Liczniki wydajności](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |zgodnie z harmonogramem, minimum 10 sekund |
| [Dziennik systemu](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |z magazynu platformy Azure: 10 minut; od agenta: po przyjeździe |
| [Dzienniki zdarzeń systemu Windows](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | po przyjeździe |


## <a name="configuring-data-sources"></a>Konfigurowanie źródeł danych
Źródła danych można skonfigurować z menu **Dane** w **obszarze ustawień zaawansowanych** dla obszaru roboczego.  Każda konfiguracja jest dostarczana do wszystkich połączonych źródeł w obszarze roboczym.  Obecnie nie można wykluczyć żadnych agentów z tej konfiguracji.

![Konfigurowanie zdarzeń systemu Windows](media/agent-data-sources/configure-events.png)

1. W witrynie Azure portal wybierz **obszary robocze usługi Log Analytics** > obszarze roboczym > ustawienia **zaawansowane**.
2. Wybierz **pozycję Dane**.
3. Kliknij źródło danych, które chcesz skonfigurować.
4. Kliknij łącze do dokumentacji dla każdego źródła danych w powyższej tabeli, aby uzyskać szczegółowe informacje na temat ich konfiguracji.


## <a name="data-collection"></a>Zbieranie danych
Konfiguracje źródeł danych są dostarczane do agentów, które są bezpośrednio połączone z usługą Azure Monitor w ciągu kilku minut.  Określone dane są zbierane od agenta i dostarczane bezpośrednio do usługi Azure Monitor w odstępach czasu specyficznych dla każdego źródła danych.  Zobacz dokumentację dla każdego źródła danych dla tych szczegółów.

W przypadku agentów programu System Center Operations Manager w połączonej grupie zarządzania konfiguracje źródeł danych są tłumaczone na pakiety administracyjne i domyślnie dostarczane do grupy zarządzania co 5 minut.  Agent pobiera pakiet administracyjny jak każdy inny i zbiera określone dane. W zależności od źródła danych dane będą wysyłane do serwera zarządzania, który przekazuje dane do usługi Azure Monitor lub agent wyśle dane do usługi Azure Monitor bez przechodzenia przez serwer zarządzania. Szczegółowe [informacje na temat monitorowania rozwiązań na platformie Azure można](../insights/solutions-inventory.md) znaleźć w szczegółowe informacje.  Możesz przeczytać o szczegółach łączenia programu Operations Manager i usługi Azure Monitor oraz modyfikowania częstotliwości dostarczania konfiguracji w [programie Configure Integration with System Center Operations Manager](om-agents.md).

Jeśli agent nie może połączyć się z usługą Azure Monitor lub program operations manager, będzie nadal zbierać dane, które dostarczy po nawiązaniu połączenia.  Dane mogą zostać utracone, jeśli ilość danych osiągnie maksymalny rozmiar pamięci podręcznej dla klienta lub jeśli agent nie jest w stanie ustanowić połączenia w ciągu 24 godzin.

## <a name="log-records"></a>Rekordy dziennika
Wszystkie dane dziennika zebrane przez usługę Azure Monitor są przechowywane w obszarze roboczym jako rekordy.  Rekordy zbierane przez różne źródła danych będą miały własny zestaw właściwości i będą identyfikowane przez ich **właściwość Type.**  Szczegółowe informacje na temat każdego typu rekordu można znaleźć w dokumentacji dla każdego źródła danych i rozwiązania.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [rozwiązaniach do monitorowania,](../insights/solutions.md) które dodają funkcje do usługi Azure Monitor, a także zbierają dane do obszaru roboczego.
* Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md) do analizowania danych zebranych ze źródeł danych i rozwiązań do monitorowania.  
* Skonfiguruj [alerty,](alerts-overview.md) aby proaktywnie powiadamiać o krytycznych danych zebranych ze źródeł danych i rozwiązań do monitorowania.
