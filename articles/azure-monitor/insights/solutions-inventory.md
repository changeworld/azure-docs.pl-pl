---
title: Szczegóły zbierania danych dla rozwiązań do zarządzania na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązania do zarządzania na platformie Azure to zbiór reguł nabycia logiki, wizualizacji i danych, które udostępniają metryki dotyczące określonego obszaru problemów.  Ten artykuł zawiera listę dostępnych rozwiązań do zarządzania od firmy Microsoft i szczegółowe informacje o ich metody i częstotliwości zbierania danych.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: 6da2b63b7a9c8dc490bd30bb02ae6e7d932b2f7b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596001"
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Szczegóły zbierania danych dla rozwiązań do zarządzania na platformie Azure
Ten artykuł zawiera listę [rozwiązań do zarządzania](solutions.md) udostępnianych przez firmę Microsoft wraz z łączami do ich szczegółowej dokumentacji.  Zawiera także informacje o ich metody i częstotliwość zbierania danych do usługi Azure Monitor.  Identyfikowanie różnych rozwiązaniach dostępnych i zrozumienie wymagań połączenia i przepływu danych dla rozwiązań do zarządzania różnych, można użyć informacji w tym artykule. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-management-solutions"></a>Lista rozwiązań do zarządzania

W poniższej tabeli wymieniono [rozwiązań do zarządzania](solutions.md) na platformie Azure obsługiwane przez firmę Microsoft. Wpis w kolumnie oznacza, że rozwiązanie zbiera dane do usługi Azure Monitor przy użyciu tej metody.  Jeśli rozwiązanie ma nie wybrano kolumn, następnie go zapisuje bezpośrednio do usługi Azure Monitor z inną usługę platformy Azure. Użyj linku dla każdej z nich do jego szczegółowej dokumentacji, aby uzyskać więcej informacji.

Wyjaśnienie kolumn są następujące:

- **Agent monitorowania Microsoft** -Agent używany w systemach Windows i Linux do uruchamiania pakietu zarządzania z rozwiązania programu SCOM i zarządzania na platformie Azure. W tej konfiguracji agenta jest podłączony bezpośrednio do usługi Azure Monitor bez połączenia z grupą zarządzania programu Operations Manager. 
- **Operations Manager** — identyczne agenta jako Microsoft monitoring agent. W tej konfiguracji ma ona [połączonych z grupą zarządzania programu Operations Manager](../../azure-monitor/platform/om-agents.md) podłączonego do usługi Azure Monitor. 
-  **Usługa Azure Storage** — rozwiązanie zbiera dane z konta usługi Azure storage. 
- **Wymagane programu Operations Manager?** -Podłączonej grupy zarządzania programu Operations Manager jest wymagany do zbierania danych przez rozwiązanie do zarządzania. 
- **Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania** — jeśli agent jest [podłączonej do grupy zarządzania SCOM](../../azure-monitor/platform/om-agents.md), a następnie przesyłane do usługi Azure Monitor z serwera zarządzania. W takim przypadku agent nie musi połączyć się bezpośrednio z usługi Azure Monitor. Jeśli to pole nie jest zaznaczone, następnie dane będą wysyłane z agenta bezpośrednio do usługi Azure Monitor nawet wtedy, gdy agent jest połączony z grupą zarządzania programu SCOM. Musisz mieć możliwość komunikacji do usługi Azure Monitor za pośrednictwem [bramy usługi Log Analytics](../../azure-monitor/platform/gateway.md).
- **Częstotliwość zbierania** -określa częstotliwość, dane są zbierane przez rozwiązanie do zarządzania. 



| **Rozwiązanie do zarządzania** | **Platforma** | **Agent monitorowania firmy Microsoft** | **Agent programu Operations Manager** | **Magazyn platformy Azure** | **Wymagane programu Operations Manager?** | **Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania** | **Częstotliwość zbierania** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Analiza dziennika aktywności](../../azure-monitor/platform/collect-activity-logs.md) | Azure | | | | | | na powiadomienie |
| [Ocena usługi AD](../../azure-monitor/insights/ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dni |
| [Stan replikacji usługi AD](../../azure-monitor/insights/ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dni |
| [Kondycja agenta](solution-agenthealth.md) | System Windows i Linux | &#8226; | &#8226; | | | &#8226; | 1 min |
| [Zgłoś alert, zarządzanie](../../azure-monitor/platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |Po dostarczeniu |
| [Zgłoś alert, zarządzanie](../../azure-monitor/platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 min |
| [Zgłoś alert, zarządzanie](../../azure-monitor/platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuty |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | Nie dotyczy |
| [Łącznik usługi Application Insights (przestarzałe)](../../azure-monitor/platform/app-insights-connector.md) | Azure | | | |  |  | na powiadomienie |
| [Automatyzacja hybrydowego procesu roboczego](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | Nie dotyczy |
| [Usługi Azure Application Gateway Analytics](../../azure-monitor/insights/azure-networking-analytics.md) | Azure |  |  |  |  |  | na powiadomienie |
| **Rozwiązanie do zarządzania** | **Platforma** | **Agent monitorowania firmy Microsoft** | **Agent programu Operations Manager** | **Magazyn platformy Azure** | **Wymagane programu Operations Manager?** | **Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania** | **Częstotliwość zbierania** |
| [Analiza sieciowej grupy zabezpieczeń platformy Azure (przestarzałe)](../../azure-monitor/insights/azure-networking-analytics.md) | Azure |  |  |  |  |  | na powiadomienie |
| [Usługi Azure SQL Analytics (wersja zapoznawcza)](../../azure-monitor/insights/azure-sql.md) | Windows | | | | | | 1 min |
| [Tworzenie kopii zapasowych](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | na powiadomienie |
| [Pojemność i wydajność (wersja zapoznawcza)](../../azure-monitor/insights/capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |Po dostarczeniu |
| [Śledzenie zmian](../../automation/automation-change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[Różni się](../../automation/automation-change-tracking.md#change-tracking-data-collection-details) |
| [Śledzenie zmian](../../automation/automation-change-tracking.md) |Linux |&#8226; | | | | |[Różni się](../../automation/automation-change-tracking.md#change-tracking-data-collection-details) |
| [Containers](../../azure-monitor/insights/containers.md) | System Windows i Linux | &#8226; | &#8226; |  |  |  | 3 minuty |
| [Analiza usługi Key Vault](../../azure-monitor/insights/azure-key-vault.md) |Windows | | | | | |na powiadomienie |
| [Ocena złośliwego oprogramowania](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |co godzinę |
| [Monitor wydajności sieci](../../azure-monitor/insights/network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Uzgodnienia protokołu TCP co 5 sekund, dane wysyłane co 3 minuty |
| [Analiza usługi Office 365 (wersja zapoznawcza)](solution-office-365.md) |Windows | | | | | |na powiadomienie |
| **Rozwiązanie do zarządzania** | **Platforma** | **Agent monitorowania firmy Microsoft** | **Agent programu Operations Manager** | **Magazyn platformy Azure** | **Wymagane programu Operations Manager?** | **Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania** | **Częstotliwość zbierania** |
| [Analiza usługi Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minut |
| [Mapa usługi](../../azure-monitor/insights/service-map.md) | System Windows i Linux | &#8226; | &#8226; |  |  |  | 15 sekund |
| [Ocena serwera SQL](../../azure-monitor/insights/sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dni |
| [SurfaceHub](../../azure-monitor/insights/surface-hubs.md) |Windows |&#8226; | | | | |Po dostarczeniu |
| [Ocena programu System Center Operations Manager (wersja zapoznawcza)](../../azure-monitor/insights/scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | 7 dni |
| [Zarządzanie aktualizacjami](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |co najmniej 2 razy dziennie i 15 minut po zainstalowaniu aktualizacji |
| [Gotowość do uaktualnienia](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dni |
| [Monitorowanie VMware (przestarzałe)](../../azure-monitor/insights/vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuty |
| [Dane o komunikacji sieciowej 2.0 (wersja zapoznawcza)](../../azure-monitor/insights/wire-data.md) |Windows (2012 R2 / 8.1 lub nowszy) |&#8226; |&#8226; | | | | 1 min |




## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [tworzenia zapytań](../../azure-monitor/log-query/log-query-overview.md) analizować dane zbierane przez rozwiązania do zarządzania.
