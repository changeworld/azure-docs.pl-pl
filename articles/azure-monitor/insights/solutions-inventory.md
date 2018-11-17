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
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: ebbedb38256c997f39e30e27aa29139dc2496fd1
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2018
ms.locfileid: "51819257"
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Szczegóły zbierania danych dla rozwiązań do zarządzania na platformie Azure
Ten artykuł zawiera listę [rozwiązań do zarządzania](solutions.md) udostępnianych przez firmę Microsoft wraz z łączami do ich szczegółowej dokumentacji.  Zawiera także informacje o ich metody i częstotliwość zbierania danych w usłudze Log Analytics.  Identyfikowanie różnych rozwiązaniach dostępnych i zrozumienie wymagań połączenia i przepływu danych dla rozwiązań do zarządzania różnych, można użyć informacji w tym artykule. 

## <a name="list-of-management-solutions"></a>Lista rozwiązań do zarządzania

W poniższej tabeli wymieniono [rozwiązań do zarządzania](solutions.md) na platformie Azure obsługiwane przez firmę Microsoft. Wpis w kolumnie oznacza, że rozwiązanie zbiera dane w usłudze Log Analytics przy użyciu tej metody.  Jeśli rozwiązanie ma nie wybrano kolumn, następnie go zapisuje bezpośrednio do usługi Log Analytics z inną usługę platformy Azure. Użyj linku dla każdej z nich do jego szczegółowej dokumentacji, aby uzyskać więcej informacji.

Wyjaśnienie kolumn są następujące:

- **Agent monitorowania Microsoft** -Agent używany w systemach Windows i Linux do uruchamiania pakietu zarządzania z rozwiązania programu SCOM i zarządzania na platformie Azure. W tej konfiguracji agenta jest podłączony bezpośrednio do usługi Log Analytics bez połączenia z grupą zarządzania programu Operations Manager. 
- **Operations Manager** — identyczne agenta jako Microsoft monitoring agent. W tej konfiguracji ma ona [połączonych z grupą zarządzania programu Operations Manager](../../log-analytics/log-analytics-om-agents.md) połączoną z usługą Log Analytics. 
-  **Usługa Azure Storage** — rozwiązanie zbiera dane z konta usługi Azure storage. 
- **Wymagane programu Operations Manager?** -Podłączonej grupy zarządzania programu Operations Manager jest wymagany do zbierania danych przez rozwiązanie do zarządzania. 
- **Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania** — jeśli agent jest [podłączonej do grupy zarządzania SCOM](../../log-analytics/log-analytics-om-agents.md), a następnie przesyłane do usługi Log Analytics z serwera zarządzania. W takim przypadku agent nie musi połączyć się bezpośrednio do usługi Log Analytics. Jeśli to pole nie jest zaznaczone, następnie dane będą wysyłane z agenta bezpośrednio do usługi Log Analytics nawet wtedy, gdy agent jest połączony z grupą zarządzania programu SCOM. Musisz mieć możliwość komunikacji z usługą Log Analytics za pośrednictwem [bramy usługi Log Analytics](../../log-analytics/log-analytics-oms-gateway.md).
- **Częstotliwość zbierania** -określa częstotliwość, dane są zbierane przez rozwiązanie do zarządzania. 



| **Rozwiązanie do zarządzania** | **Platforma** | **Agent monitorowania firmy Microsoft** | **Agent programu Operations Manager** | **Magazyn platformy Azure** | **Wymagane programu Operations Manager?** | **Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania** | **Częstotliwość zbierania** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Activity Log Analytics](../../log-analytics/log-analytics-activity.md) | Azure | | | | | | na powiadomienie |
| [Ocena usługi AD](../../log-analytics/log-analytics-ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dni |
| [Stan replikacji usługi AD](../../log-analytics/log-analytics-ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dni |
| [Kondycja agenta](solution-agenthealth.md) | System Windows i Linux | &#8226; | &#8226; | | | &#8226; | 1 min |
| [Zgłoś alert, zarządzanie](../../log-analytics/log-analytics-solution-alert-management.md) (Nagios) |Linux |&#8226; | | | | |Po dostarczeniu |
| [Zgłoś alert, zarządzanie](../../log-analytics/log-analytics-solution-alert-management.md) (Zabbix) |Linux |&#8226; | | | | |1 min |
| [Zgłoś alert, zarządzanie](../../log-analytics/log-analytics-solution-alert-management.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuty |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | Nie dotyczy |
| [Łącznik usługi Application Insights (wersja zapoznawcza)](../../log-analytics/log-analytics-app-insights-connector.md) | Azure | | | |  |  | na powiadomienie |
| [Automatyzacja hybrydowego procesu roboczego](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | Nie dotyczy |
| [Usługi Azure Application Gateway Analytics](../../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | na powiadomienie |
| **Rozwiązanie do zarządzania** | **Platforma** | **Agent monitorowania firmy Microsoft** | **Agent programu Operations Manager** | **Magazyn platformy Azure** | **Wymagane programu Operations Manager?** | **Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania** | **Częstotliwość zbierania** |
| [Analiza sieciowej grupy zabezpieczeń platformy Azure (przestarzałe)](../../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | na powiadomienie |
| [Usługi Azure SQL Analytics (wersja zapoznawcza)](../../log-analytics/log-analytics-azure-sql.md) | Windows | | | | | | 1 min |
| [Tworzenie kopii zapasowych](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | na powiadomienie |
| [Pojemność i wydajność (wersja zapoznawcza)](../../log-analytics/log-analytics-capacity.md) |Windows |&#8226; |&#8226; | | |&#8226; |Po dostarczeniu |
| [Śledzenie zmian](../../automation/automation-change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |co godzinę |
| [Śledzenie zmian](../../automation/automation-change-tracking.md) |Linux |&#8226; | | | | |co godzinę |
| [Containers](../../log-analytics/log-analytics-containers.md) | System Windows i Linux | &#8226; | &#8226; |  |  |  | 3 minuty |
| [Analiza usługi Key Vault](../../log-analytics/log-analytics-azure-key-vault.md) |Windows | | | | | |na powiadomienie |
| [Ocena złośliwego oprogramowania](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |co godzinę |
| [Monitor wydajności sieci](../../log-analytics/log-analytics-network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Uzgodnienia protokołu TCP co 5 sekund, dane wysyłane co 3 minuty |
| [Analiza usługi Office 365 (wersja zapoznawcza)](solution-office-365.md) |Windows | | | | | |na powiadomienie |
| **Rozwiązanie do zarządzania** | **Platforma** | **Agent monitorowania firmy Microsoft** | **Agent programu Operations Manager** | **Magazyn platformy Azure** | **Wymagane programu Operations Manager?** | **Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania** | **Częstotliwość zbierania** |
| [Analiza usługi Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minut |
| [Mapa usługi](../../azure-monitor/insights/service-map.md) | System Windows i Linux | &#8226; | &#8226; |  |  |  | 15 sekund |
| [Ocena serwera SQL](../../log-analytics/log-analytics-sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dni |
| [SurfaceHub](../../log-analytics/log-analytics-surface-hubs.md) |Windows |&#8226; | | | | |Po dostarczeniu |
| [Ocena programu System Center Operations Manager (wersja zapoznawcza)](../../log-analytics/log-analytics-scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | 7 dni |
| [Zarządzanie aktualizacjami](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |co najmniej 2 razy dziennie i 15 minut po zainstalowaniu aktualizacji |
| [Gotowość do uaktualnienia](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dni |
| [Monitorowanie VMware (przestarzałe)](../../log-analytics/log-analytics-vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuty |
| [Dane o komunikacji sieciowej 2.0 (wersja zapoznawcza)](../../log-analytics/log-analytics-wire-data.md) |Windows (2012 R2 / 8.1 lub nowszy) |&#8226; |&#8226; | | | | 1 min |




## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [tworzenia zapytań](../../log-analytics/log-analytics-queries.md) analizować dane zbierane przez rozwiązania do zarządzania.
