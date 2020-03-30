---
title: Spis rozwiązań do monitorowania na platformie Azure | Dokumenty firmy Microsoft
description: Rozwiązania do monitorowania w usłudze Azure Monitor to zbiór reguł logiki, wizualizacji i pozyskiwania danych, które zapewniają metryki przestawne wokół określonego obszaru problemu.  Ten artykuł zawiera listę rozwiązań do monitorowania dostępnych od firmy Microsoft oraz szczegółowe informacje na temat ich metody i częstotliwości zbierania danych.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: 7b88d957bce45bf518fc77584f1691de8010459a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663134"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Szczegóły dotyczące zapasów i zbierania danych dotyczących rozwiązań do monitorowania na platformie Azure
[Rozwiązania do monitorowania](solutions.md) wykorzystują usługi na platformie Azure, aby zapewnić dodatkowy wgląd w działanie określonej aplikacji lub usługi. Rozwiązania do monitorowania zazwyczaj zbierają dane dziennika i dostarczają zapytań i widoków do analizowania zebranych danych. Rozwiązania do monitorowania można dodać do usługi Azure Monitor dla wszystkich używanych aplikacji i usług. Są one zazwyczaj dostępne bez żadnych kosztów, ale zbierają dane, które mogą wywoływać opłaty za użycie.

Ten artykuł zawiera listę [rozwiązań montioring](solutions.md) dostępnych od firmy Microsoft z łączami do ich szczegółowej dokumentacji.  Zawiera również informacje na temat ich metody i częstotliwości zbierania danych do usługi Azure Monitor.  Informacje zawarte w tym artykule można wykorzystać do zidentyfikowania różnych dostępnych rozwiązań oraz do zrozumienia wymagań dotyczących przepływu danych i połączeń dla różnych rozwiązań monitorowania.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Lista rozwiązań monitorujących

W poniższej tabeli wymieniono [rozwiązania do monitorowania](solutions.md) na platformie Azure dostarczone przez firmę Microsoft. Wpis w kolumnie oznacza, że rozwiązanie zbiera dane do usługi Azure Monitor przy użyciu tej metody.  Jeśli rozwiązanie nie ma żadnych kolumn zaznaczone, a następnie zapisuje bezpośrednio do usługi Azure Monitor z innej usługi platformy Azure. Aby uzyskać więcej informacji, kliknij łącze dla każdego z nich, aby uzyskać więcej informacji.

Objaśnienia kolumn są następujące:

- **Agent monitorowania firmy Microsoft** — agent używany w systemach Windows i Linux do uruchamiania pakietu zarządzania z firmy SCOM i monitorowania rozwiązań z platformy Azure. W tej konfiguracji agent jest połączony bezpośrednio z usługą Azure Monitor bez połączenia z grupą zarządzania programu Operations Manager. 
- **Menedżer operacji** — identyczny agent jak agent monitorujący firmy Microsoft. W tej konfiguracji jest [połączony z grupą zarządzania programu Operations Manager,](../platform/om-agents.md) która jest połączona z usługą Azure Monitor. 
-  **Usługa Azure Storage** — rozwiązanie zbiera dane z konta magazynu platformy Azure. 
- **Wymagany jest program Operations Manager?** - Do zbierania danych przez rozwiązanie monitorujące wymagana jest połączona grupa zarządzania programem Operations Manager. 
- **Dane agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania** — jeśli agent jest [połączony z grupą zarządzania SCOM,](../platform/om-agents.md)dane są wysyłane do usługi Azure Monitor z serwera zarządzania. W takim przypadku agent nie musi łączyć się bezpośrednio z usługą Azure Monitor. Jeśli to pole nie jest zaznaczone, dane są wysyłane z agenta bezpośrednio do usługi Azure Monitor, nawet jeśli agent jest połączony z grupą zarządzania SCOM. Będzie musiał być w stanie komunikować się z usługą Azure Monitor za pośrednictwem [bramy usługi Log Analytics.](../platform/gateway.md)
- **Częstotliwość zbierania** — określa częstotliwość, że dane są zbierane przez rozwiązanie monitorowania. 



| **Rozwiązanie do monitorowania** | **Platforma** | **Agent monitorujący firmy Microsoft** | **Agent programu Operations Manager** | **Magazyn platformy Azure** | **Wymagany jest program Operations Manager?** | **Dane agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania** | **Częstotliwość zbierania** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Analiza dziennika aktywności](../platform/activity-log-collect.md) | Azure | | | | | | w sprawie powiadomienia |
| [Ocena usługi AD](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dni |
| [Stan replikacji usługi AD](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dni |
| [Kondycja agenta](solution-agenthealth.md) | System Windows i Linux | &#8226; | &#8226; | | | &#8226; | 1 min |
| [Zarządzanie alertami](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |po przyjeździe |
| [Zarządzanie alertami](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 min |
| [Zarządzanie alertami](../platform/alert-management-solution.md) (menedżer operacyjny) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuty |
| [Odzyskiwanie witryny platformy Azure](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | Nie dotyczy |
| [Łącznik usługi Application Insights (przestarzały)](../platform/app-insights-connector.md) | Azure | | | |  |  | w sprawie powiadomienia |
| [Hybrydowy pracownik automatyzacji](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | Nie dotyczy |
| [Azure Application Gateway Analytics](azure-networking-analytics.md) | Azure |  |  |  |  |  | w sprawie powiadomienia |
| **Rozwiązanie do monitorowania** | **Platforma** | **Agent monitorujący firmy Microsoft** | **Agent programu Operations Manager** | **Magazyn platformy Azure** | **Wymagany jest program Operations Manager?** | **Dane agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania** | **Częstotliwość zbierania** |
| [Usługa Azure Network Security Group Analytics (przestarzała)](azure-networking-analytics.md) | Azure |  |  |  |  |  | w sprawie powiadomienia |
| [Usługa Azure SQL Analytics (wersja zapoznawcza)](azure-sql.md) | Windows | | | | | | 1 min |
| [Kopia zapasowa](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | w sprawie powiadomienia |
| [Pojemność i wydajność (wersja zapoznawcza)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |po przyjeździe |
| [Śledzenie zmian](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[Różni się](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Śledzenie zmian](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[Różni się](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Kontenery](containers.md) | System Windows i Linux | &#8226; | &#8226; |  |  |  | 3 minuty |
| [Analiza usługi Key Vault](azure-key-vault.md) |Windows | | | | | |w sprawie powiadomienia |
| [Ocena złośliwego oprogramowania](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |Godzinowe |
| [Monitor wydajności sieci](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Uzgadnia TCP co 5 sekund, dane wysyłane co 3 minuty |
| [Usługi Office 365 Analytics (wersja zapoznawcza)](solution-office-365.md) |Windows | | | | | |w sprawie powiadomienia |
| **Rozwiązanie do monitorowania** | **Platforma** | **Agent monitorujący firmy Microsoft** | **Agent programu Operations Manager** | **Magazyn platformy Azure** | **Wymagany jest program Operations Manager?** | **Dane agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania** | **Częstotliwość zbierania** |
| [Analiza usługi Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minut |
| [Mapa usługi](service-map.md) | System Windows i Linux | &#8226; | &#8226; |  |  |  | 15 sekund |
| [Ocena serwera SQL](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dni |
| [Urządzenie SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |po przyjeździe |
| [Ocena programu Operations Manager centrum systemu (wersja zapoznawcza)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | siedem dni |
| [Zarządzanie aktualizacjami](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |co najmniej 2 razy dziennie i 15 minut po zainstalowaniu aktualizacji |
| [Gotowość do uaktualnienia](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dni |
| [Monitorowanie VMware (przestarzałe)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuty |
| [Dane przewodowe 2.0 (podgląd)](wire-data.md) |Windows (2012 R2 / 8.1 lub nowsze) |&#8226; |&#8226; | | | | 1 min |




## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zainstalować i używać rozwiązań do monitorowania](solutions.md).
* Dowiedz się, jak [tworzyć zapytania](../log-query/log-query-overview.md) do analizowania danych zebranych przez rozwiązania monitorowania.
