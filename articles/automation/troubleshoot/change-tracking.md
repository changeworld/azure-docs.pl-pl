---
title: Rozwiązywanie problemów z usługą Azure Change Tracking
description: Dowiedz się, jak rozwiązywać problemy, korzystając z funkcji Azure Automation Change Tracking i spisu.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198534"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Rozwiązywanie problemów z rozwiązaniami Change Tracking i Inventory

## <a name="windows"></a>System Windows

### <a name="records-not-showing-windows"></a>Scenariusz: rekordy Change Tracking nie są wyświetlane dla maszyn z systemem Windows

#### <a name="issue"></a>Problem

Nie widzisz żadnych Change Tracking ani wyników spisu dla maszyn z systemem Windows, które zostały dołączone do Change Tracking.

#### <a name="cause"></a>Przyczyna

Ten błąd może mieć następujące przyczyny:

* Microsoft Monitoring Agent nie jest uruchomiona.
* Trwa blokowanie komunikacji z powrotem do konta usługi Automation.
* Nie pobrano pakietów administracyjnych dla Change Tracking.
* Dołączona maszyna wirtualna może pochodzić ze sklonowanego komputera, który nie został Sysprep z zainstalowanym Microsoft Monitoring Agent.

#### <a name="resolution"></a>Rozwiązanie

Rozwiązania opisane poniżej mogą pomóc w rozwiązaniu problemu. Jeśli nadal potrzebujesz pomocy, możesz zebrać informacje diagnostyczne i skontaktować się z pomocą techniczną. Na maszynie agenta przejdź do folderu C:\Program Files\Microsoft monitoring Agent\Agent\Tools i uruchom następujące polecenia:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Domyślnie śledzenie błędów jest włączone. Aby włączyć pełne komunikaty o błędach tak jak w poprzednim przykładzie, użyj parametru *Ver* . Aby uzyskać informacje na temat śladów, użyj *pliku inf* podczas wywoływania **StartTracing. cmd**.

##### <a name="microsoft-monitoring-agent-not-running"></a>Microsoft Monitoring Agent nie działa

Sprawdź, czy Microsoft Monitoring Agent (HealthService. exe) jest uruchomiona na maszynie.

##### <a name="communication-to-automation-account-blocked"></a>Zablokowano komunikację z kontem usługi Automation

Sprawdź Podgląd zdarzeń na maszynie i Wyszukaj wszystkie zdarzenia, w których znajduje się słowo "śledzenia zmian".

Zobacz [Automatyzowanie zasobów w centrum danych lub w chmurze przy użyciu hybrydowego procesu roboczego elementu Runbook](../automation-hybrid-runbook-worker.md#network-planning) , aby dowiedzieć się więcej o adresach i portach, które muszą być dozwolone, aby Change Tracking działały.

##### <a name="management-packs-not-downloaded"></a>Nie pobrano pakietów administracyjnych

Sprawdź, czy następujące pakiety administracyjne Change Tracking i spisu są zainstalowane lokalnie:

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>Maszyna wirtualna ze sklonowanego komputera, która nie została Sysprep

W przypadku używania sklonowanego obrazu najpierw należy zainstalować obraz programu Sysprep, a następnie Microsoft Monitoring Agent.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Scenariusz: brak Change Tracking lub wyników spisu na maszynach z systemem Linux

#### <a name="issue"></a>Problem

Nie widzisz żadnych spisów ani Change Tracking wyników dla maszyn z systemem Linux, które zostały dołączone do Change Tracking. 

#### <a name="cause"></a>Przyczyna
Poniżej przedstawiono możliwe przyczyny tego problemu:
* Agent Log Analytics dla systemu Linux nie jest uruchomiony.
* Agent Log Analytics dla systemu Linux nie jest prawidłowo skonfigurowany.
* Istnieją konflikty monitorowania integralności plików (FIM).

#### <a name="resolution"></a>Rozwiązanie 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agent Log Analytics dla systemu Linux nie jest uruchomiony

Upewnij się, że demon dla Log Analytics Agent dla systemu Linux (omsagent) jest uruchomiony na komputerze. Uruchom następujące zapytanie w obszarze roboczym Log Analytics, który jest połączony z kontem usługi Automation.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Jeśli komputer nie jest wyświetlany w wynikach zapytania, nie został ostatnio zaewidencjonowany. Prawdopodobnie wystąpił problem z konfiguracją lokalną i należy ponownie zainstalować agenta. Informacje o instalacji i konfiguracji znajdują się w temacie [zbieranie danych dziennika za pomocą agenta log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Jeśli komputer jest wyświetlany w wynikach zapytania, sprawdź konfigurację zakresu. Zobacz [ukierunkowane rozwiązania monitorowania w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Aby uzyskać więcej informacji o tym problemie, zobacz artykuł dotyczący [problemu: nie widzisz żadnych danych systemu Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agent Log Analytics dla systemu Linux nie został poprawnie skonfigurowany

Agent Log Analytics dla systemu Linux może nie być prawidłowo skonfigurowany do zbierania danych wyjściowych dziennika i wiersza polecenia przy użyciu narzędzia zbierającego dzienniki pakietu OMS. Zobacz [śledzenie zmian w środowisku przy użyciu rozwiązania Change Tracking](../change-tracking.md).

##### <a name="fim-conflicts"></a>Konflikty programu FIM

Funkcja FIM Azure Security Center może nieprawidłowo sprawdzać integralność plików systemu Linux. Sprawdź, czy kod FIM działa i poprawnie skonfigurowany do monitorowania plików systemu Linux. Zobacz [śledzenie zmian w środowisku przy użyciu rozwiązania Change Tracking](../change-tracking.md).

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz swojego problemu lub nie możesz rozwiązać problemu, użyj jednego z poniższych kanałów, aby uzyskać więcej pomocy.

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
