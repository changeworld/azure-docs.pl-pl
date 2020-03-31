---
title: Rozwiązywanie problemów z śledzeniem zmian usługi Azure
description: Dowiedz się, jak rozwiązywać problemy i rozwiązywać problemy za pomocą funkcji Śledzenia zmian i spisu usług Azure Automation.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198534"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Rozwiązywanie problemów z rozwiązaniami Change Tracking i Inventory

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scenariusz: rekordy śledzenia zmian nie są wyświetlane dla komputerów z systemem Windows

#### <a name="issue"></a>Problem

Nie widać żadnych wyników śledzenia zmian ani zapasów dla komputerów z systemem Windows, które są wbudowane do śledzenia zmian.

#### <a name="cause"></a>Przyczyna

Ten błąd może mieć następujące przyczyny:

* Agent monitorowania firmy Microsoft nie jest uruchomiony.
* Komunikacja z powrotem do konta automatyzacji jest blokowana.
* Pakiety administracyjne śledzenia zmian nie są pobierane.
* Maszyna wirtualna jest wbudowany może pochodzić z sklonowanego komputera, który nie był sysprepped z microsoft agenta monitorowania zainstalowany.

#### <a name="resolution"></a>Rozwiązanie

Rozwiązania opisane poniżej mogą pomóc w rozwiązaniu problemu. Jeśli nadal potrzebujesz pomocy, możesz zbierać informacje diagnostyczne i kontaktować się z pomocą techniczną. Na komputerze agenta przejdź do pozycji C:\Program Files\Microsoft Monitoring Agent\Agent\Tools i uruchom następujące polecenia:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Domyślnie śledzenie błędów jest włączone. Aby włączyć pełne komunikaty o błędach, jak w poprzednim przykładzie, należy użyć parametru *VER.* Aby uzyskać informacje śledzenia, należy użyć *pliku INF* podczas wywoływania **pliku StartTracing.cmd**.

##### <a name="microsoft-monitoring-agent-not-running"></a>Nie jest uruchomiony agent monitorujący firmy Microsoft

Sprawdź, czy agent monitorowania firmy Microsoft (HealthService.exe) jest uruchomiony na komputerze.

##### <a name="communication-to-automation-account-blocked"></a>Zablokowano komunikację z kontem automatyzacji

Sprawdź Podgląd zdarzeń na komputerze i poszukaj wszystkich zdarzeń, które mają w sobie słowo "changetracking".

Zobacz [Automatyzacja zasobów w centrum danych lub w chmurze przy użyciu hybrydowego środowiska roboczego żyła,](../automation-hybrid-runbook-worker.md#network-planning) aby dowiedzieć się więcej o adresach i portach, które muszą być dozwolone, aby śledzenie zmian działało.

##### <a name="management-packs-not-downloaded"></a>Nie pobrano pakietów administracyjnych

Sprawdź, czy następujące pakiety zarządzania śledzeniem zmian i zapasami są zainstalowane lokalnie:

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>Maszyna wirtualna ze sklonowanej maszyny, która nie została sysprepped

Jeśli używasz sklonowanego obrazu, najpierw przesuń obraz, a następnie zainstaluj agenta monitorowania firmy Microsoft.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Scenariusz: Brak śledzenia zmian lub wyniki zapasów na komputerach z systemem Linux

#### <a name="issue"></a>Problem

Nie widzisz żadnych wyników śledzenia zapasów lub zmian dla maszyn z systemem Linux, które są wbudowane do śledzenia zmian. 

#### <a name="cause"></a>Przyczyna
Oto możliwe przyczyny specyficzne dla tego problemu:
* Agent analizy dzienników dla systemu Linux nie jest uruchomiony.
* Agent usługi Log Analytics dla systemu Linux nie jest poprawnie skonfigurowany.
* Występują konflikty monitorowania integralności plików (FIM).

#### <a name="resolution"></a>Rozwiązanie 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agent analizy dzienników dla systemu Linux nie jest uruchomiony

Sprawdź, czy demon dla agenta usługi Log Analytics dla systemu Linux (omsagent) jest uruchomiony na komputerze. Uruchom następującą kwerendę w obszarze roboczym usługi Log Analytics, która jest połączona z kontem automatyzacji.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Jeśli komputer nie jest widoczny w wynikach kwerendy, nie został ostatnio zaewidencjonowany. Prawdopodobnie występuje problem z konfiguracją lokalną i należy ponownie zainstalować agenta. Aby uzyskać informacje o instalacji i konfiguracji, zobacz [Zbieranie danych dziennika za pomocą agenta usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Jeśli komputer pojawia się w wynikach kwerendy, sprawdź konfigurację zakresu. Zobacz [rozwiązania do monitorowania kierowania w usłudze Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)

Aby uzyskać więcej rozwiązywania tego problemu, zobacz [Problem: Nie widzisz żadnych danych systemu Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agent usługi Log Analytics dla systemu Linux nie został poprawnie skonfigurowany

Agent usługi Log Analytics dla systemu Linux może nie być poprawnie skonfigurowany dla kolekcji danych wyjściowych dziennika i wiersza polecenia przy użyciu narzędzia Moduł zbierający dzienniki OMS. Zobacz [Śledzenie zmian w środowisku za pomocą rozwiązania śledzenia zmian](../change-tracking.md).

##### <a name="fim-conflicts"></a>Konflikty FIM

Funkcja FIM usługi Azure Security Center może niepoprawnie sprawdzać integralność plików systemu Linux. Sprawdź, czy FIM działa i poprawnie skonfigurowany do monitorowania plików systemu Linux. Zobacz [Śledzenie zmian w środowisku za pomocą rozwiązania śledzenia zmian](../change-tracking.md).

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, skorzystaj z jednego z następujących kanałów, aby uzyskać większą pomoc techniczną.

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.
