---
title: Szczegóły pobierania danych rozwiązań zarządzania Azure | Dokumenty Microsoft
description: Rozwiązania do zarządzania na platformie Azure są Kolekcja reguł nabycia logiki, wizualizacji i danych zawierających metryki przestawiać wokół obszaru określonego problemu.  Ten artykuł zawiera listę dostępnych rozwiązań zarządzania firmy Microsoft i szczegółowe informacje dotyczące ich metody i częstotliwości zbierania danych.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: bwren
ms.openlocfilehash: ab07a11883b3462c4b9d0f9adab6c55e4fe49d78
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Szczegóły pobierania danych rozwiązań zarządzania Azure
Ten artykuł zawiera listę [rozwiązań w zakresie zarządzania](monitoring-solutions.md) udostępniana przez firmę Microsoft z łączami do ich szczegółowej dokumentacji.  Zawiera także informacje o ich metody i częstotliwości zbierania danych do analizy dzienników.  Do identyfikowania różnych rozwiązań dostępnych i zrozumieć wymagania przepływu i połączenia danych dla rozwiązań w zakresie zarządzania różnych, można użyć informacje w tym artykule. 

## <a name="list-of-management-solutions"></a>Lista rozwiązań w zakresie zarządzania

W poniższej tabeli wymieniono [rozwiązań w zakresie zarządzania](monitoring-solutions.md) w Azure dostarczony przez firmę Microsoft. Wpis w kolumnie oznacza, że rozwiązanie zbiera dane do analizy dzienników przy użyciu tej metody.  Jeśli rozwiązanie nie ma kolumn zaznaczone, następnie zapisuje bezpośrednio do analizy dzienników z inną usługę Azure. Kliknięcie łącza dla każdego z nich do jego szczegółowej dokumentacji, aby uzyskać więcej informacji.

Wyjaśnienia kolumn są w następujący sposób:

- **Program Microsoft agent monitorowania** -agenta używane w systemach Windows i Linux, aby uruchomić pakiet zarządy z SCOM i zarządzania rozwiązania firmy Azure. W tej konfiguracji agenta jest podłączony bezpośrednio do analizy dzienników bez połączenia z grupą zarządzania Operations Manager. 
- **Menedżera operacji** -agent identyczne jak Microsoft agent monitorowania. W tej konfiguracji ma on [połączony z grupą zarządzania Operations Manager](../log-analytics/log-analytics-om-agents.md) podłączonego do analizy dzienników. 
-  **Magazyn Azure** -rozwiązanie zbiera dane z konta magazynu Azure. 
- **Wymagane programu Operations Manager?** -Podłączona grupa zarządzania programu Operations Manager jest wymagane dla zbierania danych przez rozwiązanie do zarządzania. 
- **Dane agenta menedżera operacji wysyłane za pośrednictwem grupy zarządzania** - jeśli agent jest [połączeni z grupą zarządzania SCOM](../log-analytics/log-analytics-om-agents.md), a następnie dane są przesyłane do analizy dzienników z serwera zarządzania. W takim przypadku agent nie trzeba podłączyć bezpośrednio do analizy dzienników. Jeśli to pole nie jest zaznaczone, następnie dane są przesyłane z agenta bezpośrednio do analizy dzienników nawet jeśli agent jest podłączony do grupy zarządzania SCOM. albo będzie musiała mieć możliwość komunikowania się do analizy dzienników za pomocą [OMS Brama](../log-analytics/log-analytics-oms-gateway.md).
- **Częstotliwość pobierania** -określa częstotliwość, że dane są zbierane przez rozwiązanie do zarządzania. 



| **Rozwiązanie do zarządzania** | **Platformy** | **Agent monitorowania firmy Microsoft** | **Agent programu Operations Manager** | **Magazyn platformy Azure** | **Wymagane programu Operations Manager?** | **Danych agenta programu Operations Manager są wysyłane za pośrednictwem grupy zarządzania** | **Częstotliwość kolekcji** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Activity Log Analytics](../log-analytics/log-analytics-activity.md) | Azure | | | | | | na powiadomienia |
| [Ocena usługi AD](../log-analytics/log-analytics-ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dni |
| [Stan replikacji usługi AD](../log-analytics/log-analytics-ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dni |
| [Kondycja agenta](../operations-management-suite/oms-solution-agenthealth.md) | System Windows i Linux | &#8226; | &#8226; | | | &#8226; | 1 min |
| [Alert zarządzania](../log-analytics/log-analytics-solution-alert-management.md) (Nagios) |Linux |&#8226; | | | | |po przybyciu |
| [Alert zarządzania](../log-analytics/log-analytics-solution-alert-management.md) (Zabbix) |Linux |&#8226; | | | | |1 min |
| [Alert zarządzania](../log-analytics/log-analytics-solution-alert-management.md) (kierownik) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuty |
| [Azure Site Recovery](../site-recovery/site-recovery-overview.md) | Azure | | | | | | Nie dotyczy |
| [Łącznik aplikacji Insights (wersja zapoznawcza)](../log-analytics/log-analytics-app-insights-connector.md) | Azure | | | |  |  | na powiadomienia |
| [Automatyzacja hybrydowego procesu roboczego](../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | Nie dotyczy |
| [Analiza bramy aplikacji Azure](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | na powiadomienia |
| **Rozwiązanie do zarządzania** | **Platformy** | **Agent monitorowania firmy Microsoft** | **Agent programu Operations Manager** | **Magazyn platformy Azure** | **Wymagane programu Operations Manager?** | **Danych agenta programu Operations Manager są wysyłane za pośrednictwem grupy zarządzania** | **Częstotliwość kolekcji** |
| [Grupy zabezpieczeń sieci Azure analityka](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | na powiadomienia |
| [Analiza Azure SQL (wersja zapoznawcza)](../log-analytics/log-analytics-azure-sql.md) | Windows | | | | | | 1 min |
| [Tworzenie kopii zapasowych](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | na powiadomienia |
| [Pojemność i wydajność (wersja zapoznawcza)](../log-analytics/log-analytics-capacity.md) |Windows |&#8226; |&#8226; | | |&#8226; |po przybyciu |
| [Śledzenie zmian](../log-analytics/log-analytics-change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |co godzinę |
| [Śledzenie zmian](../log-analytics/log-analytics-change-tracking.md) |Linux |&#8226; | | | | |co godzinę |
| [Containers](../log-analytics/log-analytics-containers.md) | System Windows i Linux | &#8226; | &#8226; |  |  |  | 3 minuty |
| [Analiza usługi Key Vault](../log-analytics/log-analytics-azure-key-vault.md) |Windows | | | | | |na powiadomienia |
| [Ocena złośliwego oprogramowania](../log-analytics/log-analytics-malware.md) |Windows |&#8226; |&#8226; | | |&#8226; |co godzinę |
| [Monitor wydajności sieci](../log-analytics/log-analytics-network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP uzgodnienia co 5 sekund, dane wysyłane co 3 minuty |
| [Analiza usługi Office 365 (wersja zapoznawcza)](../operations-management-suite/oms-solution-office-365.md) |Windows | | | | | |na powiadomienia |
| **Rozwiązanie do zarządzania** | **Platformy** | **Agent monitorowania firmy Microsoft** | **Agent programu Operations Manager** | **Magazyn platformy Azure** | **Wymagane programu Operations Manager?** | **Danych agenta programu Operations Manager są wysyłane za pośrednictwem grupy zarządzania** | **Częstotliwość kolekcji** |
| [Zabezpieczenia i audyt](../operations-management-suite/oms-security-getting-started.md) (Syslog) | Linux | &#8226; | | |  |  | po przybyciu |
| [Zabezpieczenia i audyt](../operations-management-suite/oms-security-getting-started.md) (dzienniki zdarzeń zabezpieczeń) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | po przybyciu |
| [Zabezpieczenia i audyt](../operations-management-suite/oms-security-getting-started.md) (zapory Dzienniki) |Windows |&#8226; |&#8226; |  |  |  |po przybyciu |
| [Usługa sieci szkieletowej Analytics (wersja zapoznawcza)](../log-analytics/log-analytics-service-fabric.md) |Windows | | |&#8226; | | |5 minut |
| [Mapa usługi](../operations-management-suite/operations-management-suite-service-map.md) | System Windows i Linux | &#8226; | &#8226; |  |  |  | 15 sekund |
| [Ocena serwera SQL](../log-analytics/log-analytics-sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dni |
| [SurfaceHub](../log-analytics/log-analytics-surface-hubs.md) |Windows |&#8226; | | | | |po przybyciu |
| [Do oceny programu System Center Operations Manager (wersja zapoznawcza)](../log-analytics/log-analytics-scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | 7 dni |
| [Zarządzanie aktualizacjami](../operations-management-suite/oms-solution-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |co najmniej 2 razy dziennie i 15 minut po zainstalowaniu aktualizacji |
| [Gotowość do uaktualnienia](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dni |
| [VMware monitorowania (wersja zapoznawcza)](../log-analytics/log-analytics-vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuty |
| [Podczas transmisji danych 2.0 (wersja zapoznawcza)](../log-analytics/log-analytics-wire-data.md) |Systemu Windows (2012 R2 / 8.1 lub nowszy) |&#8226; |&#8226; | | | | 1 min |




## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [tworzenie zapytań](../log-analytics/log-analytics-log-searches.md) do analizowania danych zebranych przez rozwiązania do zarządzania.
