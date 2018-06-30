---
title: Szczegóły zbierania danych w celu rozwiązania do zarządzania na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązania do zarządzania na platformie Azure są Kolekcja reguł nabycia logiki, wizualizacji i danych zawierających metryki przestawiać wokół obszaru określonego problemu.  Ten artykuł zawiera listę dostępnych rozwiązań do zarządzania firmy Microsoft i szczegółowe informacje o ich metody i częstotliwość zbierania danych.
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
ms.openlocfilehash: 887611f1293e276070df51ac06df51a994161ec1
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112200"
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Szczegóły zbierania danych w celu rozwiązania do zarządzania na platformie Azure
Ten artykuł zawiera listę [rozwiązań do zarządzania](monitoring-solutions.md) udostępnianych przez firmę Microsoft z łącza do ich szczegółowa dokumentacja.  Zawiera także informacje o ich metody i częstotliwość zbierania danych do analizy dzienników.  Do identyfikacji różnych rozwiązań dostępne i zrozumienie wymagań przepływu i połączenia danych rozwiązania do zarządzania różnych, możesz skorzystać z informacji w tym artykule. 

## <a name="list-of-management-solutions"></a>Lista rozwiązań do zarządzania

W poniższej tabeli wymieniono [rozwiązań do zarządzania](monitoring-solutions.md) w dostarczonym przez firmę Microsoft Azure. Wpis w kolumnie oznacza, że rozwiązanie służy do zbierania danych do analizy dzienników przy użyciu tej metody.  Jeśli rozwiązanie nie zawiera kolumn zaznaczone, następnie zapisuje bezpośrednio do analizy dzienników z innej usługi Azure. Kliknij link dla każdej z nich do jego szczegółową dokumentację, aby uzyskać więcej informacji.

Wyjaśnienia dotyczące kolumn są następujące:

- **Agent monitorowania firmy Microsoft** -Agent używane w systemach Windows i Linux do uruchamiania Management pack z rozwiązań zarządzania i programu SCOM z platformy Azure. W tej konfiguracji agenta jest podłączony bezpośrednio do analizy dzienników bez połączenia z grupą zarządzania programu Operations Manager. 
- **Operations Manager** — identyczne agenta jako programu Microsoft monitoring agent. W tej konfiguracji ma [połączony z grupą zarządzania programu Operations Manager](../log-analytics/log-analytics-om-agents.md) podłączonego do analizy dzienników. 
-  **Usługa Azure Storage** — rozwiązania zbiera dane z konta magazynu platformy Azure. 
- **Wymagane programu Operations Manager?** -Podłączonej grupy zarządzania programu Operations Manager jest wymagany do zbierania danych przez rozwiązanie do zarządzania. 
- **Danych agenta programu Operations Manager są wysyłane za pośrednictwem grupy zarządzania** — jeśli agent jest [połączony z grupą zarządzania programu SCOM](../log-analytics/log-analytics-om-agents.md), a następnie wysłaniu danych z analizą dzienników z serwera zarządzania. W takim przypadku agenta nie musi połączyć się bezpośrednio z analizy dzienników. Jeśli to pole nie jest zaznaczone, następnie dane są przesyłane z agenta bezpośrednio do analizy dzienników nawet wtedy, gdy agent jest połączony z grupą zarządzania programu SCOM. albo trzeba będzie mieć możliwości komunikacji z analizą dzienników za pośrednictwem [bramy OMS](../log-analytics/log-analytics-oms-gateway.md).
- **Częstotliwość zbierania** -częstotliwość Określa, że dane są zbierane przez rozwiązanie do zarządzania. 



| **Rozwiązanie do zarządzania** | **Platformy** | **Agent monitorowania firmy Microsoft** | **Agent programu Operations Manager** | **Magazyn platformy Azure** | **Wymagane programu Operations Manager?** | **Danych agenta programu Operations Manager są wysyłane za pośrednictwem grupy zarządzania** | **Częstotliwość kolekcji** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Activity Log Analytics](../log-analytics/log-analytics-activity.md) | Azure | | | | | | na powiadomienia |
| [Ocena usługi AD](../log-analytics/log-analytics-ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dni |
| [Stan replikacji usługi AD](../log-analytics/log-analytics-ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dni |
| [Kondycja agenta](../operations-management-suite/oms-solution-agenthealth.md) | System Windows i Linux | &#8226; | &#8226; | | | &#8226; | 1 min |
| [Alert zarządzania](../log-analytics/log-analytics-solution-alert-management.md) (Nagios) |Linux |&#8226; | | | | |po przybyciu |
| [Alert zarządzania](../log-analytics/log-analytics-solution-alert-management.md) (Zabbix) |Linux |&#8226; | | | | |1 min |
| [Alert zarządzania](../log-analytics/log-analytics-solution-alert-management.md) (programu Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuty |
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
