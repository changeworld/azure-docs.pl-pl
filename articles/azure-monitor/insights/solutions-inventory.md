---
title: Spis monitorowanie rozwiązań na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązania do monitorowania w usłudze Azure Monitor to zbiór reguł nabycia logiki, wizualizacji i danych, które udostępniają metryki dotyczące określonego obszaru problemów.  Ten artykuł zawiera listę monitorowanie rozwiązań firmy Microsoft i szczegółowe informacje o ich metody i częstotliwości zbierania danych.
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
ms.openlocfilehash: 9398815ea75c0eacd99a6e40c569254fac671cbb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234030"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Szczegóły kolekcji spisu i danych monitorowania rozwiązań na platformie Azure
[Monitorowanie rozwiązań](solutions.md) korzystać z usług platformy Azure, aby zapewnić dodatkowy wgląd w działania określonej aplikacji lub usługi. Rozwiązania do monitorowania zazwyczaj zbierają dane dzienników i zapewnić zapytań i widoków do analizowania zebranych danych. Rozwiązania do monitorowania można dodać do usługi Azure Monitor dla dowolnej aplikacji i usług, których używasz. Są one zazwyczaj dostępne w żadnych kosztów, ale zbieranie danych, które można wywołać opłaty za użycie.

Ten artykuł zawiera listę [rozwiązania montioring](solutions.md) udostępnianych przez firmę Microsoft wraz z łączami do ich szczegółowej dokumentacji.  Zawiera także informacje o ich metody i częstotliwość zbierania danych do usługi Azure Monitor.  Aby zidentyfikować różne rozwiązania, dostępne i zrozumienie wymagań połączenia i przepływu danych dla różnych rozwiązań do monitorowania, można użyć informacje w tym artykule.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Lista rozwiązania do monitorowania

W poniższej tabeli wymieniono [rozwiązania do monitorowania](solutions.md) na platformie Azure obsługiwane przez firmę Microsoft. Wpis w kolumnie oznacza, że rozwiązanie zbiera dane do usługi Azure Monitor przy użyciu tej metody.  Jeśli rozwiązanie ma nie wybrano kolumn, następnie go zapisuje bezpośrednio do usługi Azure Monitor z inną usługę platformy Azure. Użyj linku dla każdej z nich do jego szczegółowej dokumentacji, aby uzyskać więcej informacji.

Wyjaśnienie kolumn są następujące:

- **Agent monitorowania Microsoft** — Agent używane w systemach Windows i Linux do uruchamiania pakietu zarządzania z programu SCOM i monitorowania rozwiązań na platformie Azure. W tej konfiguracji agenta jest podłączony bezpośrednio do usługi Azure Monitor bez połączenia z grupą zarządzania programu Operations Manager. 
- **Operations Manager** — identyczne agenta jako Microsoft monitoring agent. W tej konfiguracji ma ona [połączonych z grupą zarządzania programu Operations Manager](../platform/om-agents.md) podłączonego do usługi Azure Monitor. 
-  **Usługa Azure Storage** — rozwiązanie zbiera dane z konta usługi Azure storage. 
- **Wymagane programu Operations Manager?** -Podłączonej grupy zarządzania programu Operations Manager jest wymagany do zbierania danych przez rozwiązanie do monitorowania. 
- **Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania** — jeśli agent jest [podłączonej do grupy zarządzania SCOM](../platform/om-agents.md), a następnie przesyłane do usługi Azure Monitor z serwera zarządzania. W takim przypadku agent nie musi połączyć się bezpośrednio z usługi Azure Monitor. Jeśli to pole nie jest zaznaczone, następnie dane będą wysyłane z agenta bezpośrednio do usługi Azure Monitor nawet wtedy, gdy agent jest połączony z grupą zarządzania programu SCOM. Musisz mieć możliwość komunikacji do usługi Azure Monitor za pośrednictwem [bramy usługi Log Analytics](../platform/gateway.md).
- **Częstotliwość zbierania** -określa częstotliwość, dane są zbierane przez rozwiązania do monitorowania. 



| **Rozwiązanie do monitorowania** | **Platforma** | **Agent monitorowania firmy Microsoft** | **Agent programu Operations Manager** | **Magazyn platformy Azure** | **Wymagane programu Operations Manager?** | **Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania** | **Częstotliwość zbierania** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Analiza dziennika aktywności](../platform/activity-log-collect.md) | Azure | | | | | | na powiadomienie |
| [Ocena usługi AD](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dni |
| [Stan replikacji usługi AD](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dni |
| [Kondycja agenta](solution-agenthealth.md) | System Windows i Linux | &#8226; | &#8226; | | | &#8226; | 1 min |
| [Zgłoś alert, zarządzanie](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |Po dostarczeniu |
| [Zgłoś alert, zarządzanie](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 min |
| [Zgłoś alert, zarządzanie](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuty |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | Nie dotyczy |
| [Łącznik usługi Application Insights (przestarzałe)](../platform/app-insights-connector.md) | Azure | | | |  |  | na powiadomienie |
| [Automatyzacja hybrydowego procesu roboczego](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | Nie dotyczy |
| [Usługi Azure Application Gateway Analytics](azure-networking-analytics.md) | Azure |  |  |  |  |  | na powiadomienie |
| **Rozwiązanie do monitorowania** | **Platforma** | **Agent monitorowania firmy Microsoft** | **Agent programu Operations Manager** | **Magazyn platformy Azure** | **Wymagane programu Operations Manager?** | **Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania** | **Częstotliwość zbierania** |
| [Analiza sieciowej grupy zabezpieczeń platformy Azure (przestarzałe)](azure-networking-analytics.md) | Azure |  |  |  |  |  | na powiadomienie |
| [Usługi Azure SQL Analytics (wersja zapoznawcza)](azure-sql.md) | Windows | | | | | | 1 min |
| [Tworzenie kopii zapasowych](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | na powiadomienie |
| [Pojemność i wydajność (wersja zapoznawcza)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |Po dostarczeniu |
| [Śledzenie zmian](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[Różni się](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Śledzenie zmian](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[Różni się](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Containers](containers.md) | System Windows i Linux | &#8226; | &#8226; |  |  |  | 3 minuty |
| [Analiza usługi Key Vault](azure-key-vault.md) |Windows | | | | | |na powiadomienie |
| [Ocena złośliwego oprogramowania](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |co godzinę |
| [Monitor wydajności sieci](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Uzgodnienia protokołu TCP co 5 sekund, dane wysyłane co 3 minuty |
| [Analiza usługi Office 365 (wersja zapoznawcza)](solution-office-365.md) |Windows | | | | | |na powiadomienie |
| **Rozwiązanie do monitorowania** | **Platforma** | **Agent monitorowania firmy Microsoft** | **Agent programu Operations Manager** | **Magazyn platformy Azure** | **Wymagane programu Operations Manager?** | **Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania** | **Częstotliwość zbierania** |
| [Analiza usługi Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minut |
| [Mapa usługi](service-map.md) | System Windows i Linux | &#8226; | &#8226; |  |  |  | 15 sekund |
| [Ocena serwera SQL](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dni |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |Po dostarczeniu |
| [Ocena programu System Center Operations Manager (wersja zapoznawcza)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | 7 dni |
| [Zarządzanie aktualizacjami](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |co najmniej 2 razy dziennie i 15 minut po zainstalowaniu aktualizacji |
| [Gotowość do uaktualnienia](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dni |
| [Monitorowanie VMware (przestarzałe)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuty |
| [Dane o komunikacji sieciowej 2.0 (wersja zapoznawcza)](wire-data.md) |Windows (2012 R2 / 8.1 lub nowszy) |&#8226; |&#8226; | | | | 1 min |




## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [Instalowanie i korzystanie z rozwiązania do monitorowania](solutions.md).
* Dowiedz się, jak [tworzenia zapytań](../log-query/log-query-overview.md) analizować dane zbierane przez rozwiązania do monitorowania.
