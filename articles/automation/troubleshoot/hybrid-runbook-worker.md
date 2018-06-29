---
title: Rozwiązywanie problemów — hybrydowymi elementami roboczymi Runbook usługi Automatyzacja Azure
description: Ten artykuł zawiera informacje o rozwiązywaniu problemów Azure Automation hybrydowymi elementami roboczymi Runbook
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1cae7253a4bfcb4f83baf003a4d9d3c367d8f014
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063917"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Rozwiązywanie problemów z hybrydowych procesów roboczych elementu Runbook

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z hybrydowych procesów roboczych elementu Runbook.

## <a name="general"></a>Ogólne

Hybrydowy proces roboczy elementu Runbook zależy od agenta do komunikowania się z Twoim kontem automatyzacji Zarejestruj pracownika, otrzymywanie zadania elementów runbook i zgłoszenia stanu. W systemie Windows ten agent jest programu Microsoft Monitoring Agent. Dla systemu Linux jest Agent pakietu OMS dla systemu Linux.

###<a name="runbook-execution-fails"></a>Scenariusz: Wykonanie elementu Runbook nie powiodło się.

#### <a name="issue"></a>Problem

Wykonanie elementu Runbook nie powiedzie się i zostanie wyświetlony następujący błąd:

```
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Element runbook został wstrzymany, wkrótce po próby wykonania jej trzy razy. Istnieją warunki, które mogą przerwać pomyślne zakończenie działania elementu runbook i powiązane komunikat nie ma żadnych dodatkowych informacji i wskazujący przyczynę.

#### <a name="cause"></a>Przyczyna

Potencjalne możliwe przyczyny są następujące:

* Elementy runbook nie mogą uwierzytelnić się przy użyciu lokalnych zasobów

* Hybrydowy proces roboczy jest używany serwer proxy lub Zapora

* Elementy runbook nie mogą uwierzytelnić się przy użyciu lokalnych zasobów

* Wyznaczony do uruchomienia funkcji hybrydowego procesu roboczego elementu Runbook komputer spełnia minimalne wymagania sprzętowe.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy komputer ma dostęp ruchu wychodzącego do *.azure automation.net na porcie 443.

Komputery z systemem hybrydowy proces roboczy elementu Runbook powinna spełniać minimalne wymagania sprzętowe przed oznaczeniem go do obsługi tej funkcji. W przeciwnym razie w zależności od innych procesów w tle i rywalizacji spowodowane przez elementy runbook podczas wykonywania na wykorzystanie zasobów komputera staje się nadmiernie wykorzystywany i spowodować opóźnienia zadania elementu runbook i przekroczeń limitu czasu.

Upewnij się, że komputer wyznaczony do uruchomienia funkcji hybrydowego procesu roboczego elementu Runbook spełnia minimalne wymagania sprzętowe. Jeśli tak, monitorowanie wykorzystania procesora CPU i pamięci, aby określić korelacja wydajności procesów hybrydowy proces roboczy elementu Runbook i Windows. Brak pamięci lub dużego wykorzystania procesora CPU, może to oznaczać konieczność uaktualnienia lub dodać dodatkowych procesorów lub zwiększ ilość pamięci do adresów wąskie gardło zasobów i Usuń przyczynę błędu. Opcjonalnie zaznacz zasób różnych obliczeń, który może obsługiwać minimalne wymagania i skalowania, gdy wymaganego obciążenia wskazywać wzrost jest konieczne.

Sprawdź **Microsoft SMA** odpowiednie zdarzenie z opisem w dzienniku zdarzeń *Win32 proces został zakończony z kodem [4294967295]*. Przyczyną tego błędu jest nie jeszcze skonfigurowane uwierzytelnianie w elementach runbook lub określony poświadczeń Uruchom jako grupy hybrydowych procesów roboczych. Przegląd [uprawnienia elementów Runbook](../automation-hrw-run-runbooks.md#runbook-permissions) o potwierdzenie zostały prawidłowo skonfigurowane uwierzytelnianie na elementach runbook.

## <a name="linux"></a>Linux

Linux hybrydowy proces roboczy jest zależna od agenta pakietu OMS dla systemu Linux do komunikowania się z Twoim kontem automatyzacji Zarejestruj pracownika, otrzymywanie zadania elementów runbook i zgłoszenia stanu. W przypadku niepowodzenia rejestracji pracownika poniżej przedstawiono niektóre możliwe przyczyny błędu:

###<a name="oms-agent-not-running"></a>Scenariusz: Agent pakietu OMS dla systemu Linux nie jest uruchomiona

Jeśli nie jest uruchomiony Agent pakietu OMS dla systemu Linux, zapobiega to Linux hybrydowy proces roboczy komunikację z usługi Automatyzacja Azure. Sprawdź, agent nie działa, wprowadzając następujące polecenie: `ps -ef | grep python`. Powinny pojawić się dane wyjściowe podobne do następujących procesów python z **nxautomation** konta użytkownika. Jeśli rozwiązania zarządzania aktualizacjami lub automatyzacji Azure nie są włączone, żaden z następujące procesy są uruchomione.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Na poniższej liście przedstawiono procesy, które są uruchamiane dla procesu roboczego elementu Runbook dla hybrydowych w systemie Linux. Znajdują się one w `/var/opt/microsoft/omsagent/state/automationworker/` katalogu.

* **OMS.conf** — jest to proces roboczy manager, to jest uruchamiany bezpośrednio z usługi Konfiguracja DSC.

* **Worker.conf** — ten proces jest automatycznie zarejestrowane hybrydowego procesu roboczego, jest uruchomiona przez Menedżera procesu roboczego. Ten proces jest używany przez zarządzanie aktualizacją i działa w sposób niewidoczny dla użytkownika. Ten proces jest nieobecna, jeśli rozwiązanie do zarządzania aktualizacji nie jest włączona na tym komputerze.

* **diy/Worker.conf** — ten proces jest DIY hybrydowego procesu roboczego. DIY hybrydowy proces roboczy jest używany do wykonywania elementów runbook użytkownika na hybrydowy proces roboczy elementu Runbook. Tylko różni się od automatycznie zarejestrowane hybrydowego procesu roboczego szczegółowo klucza, która jest używana z innej konfiguracji. Ten proces jest nieobecna, jeśli nie włączono rozwiązania Automatyzacja Azure i DIY hybrydowy proces roboczy systemu Linux nie jest zarejestrowany.

Jeśli Agent pakietu OMS dla systemu Linux nie jest uruchomiona, uruchom następujące polecenie, aby uruchomić usługę: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

###<a name="class-does-not-exist"></a>Scenariusz: Określona klasa nie istnieje.

Jeśli zostanie wyświetlony błąd: **określonej klasy nie istnieje.** w `/var/opt/microsoft/omsconfig/omsconfig.log` , a następnie Agent pakietu OMS dla systemu Linux, musi zostać zaktualizowany. Uruchom następujące polecenie, aby ponownie zainstalować agenta pakietu OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows hybrydowy proces roboczy jest zależna od programu Microsoft Monitoring Agent, aby komunikować się z Twoim kontem automatyzacji Zarejestruj pracownika, otrzymywanie zadania elementów runbook i zgłoszenia stanu. W przypadku niepowodzenia rejestracji pracownika poniżej przedstawiono niektóre możliwe przyczyny błędu:

###<a name="mma-not-running"></a>Scenariusz: Microsoft Monitoring Agent nie jest uruchomiony

#### <a name="issue"></a>Problem

`healthservice` Usługa nie jest uruchomiona na maszynie hybrydowy proces roboczy elementu Runbook.

#### <a name="cause"></a>Przyczyna

Jeśli usługa Microsoft Monitoring Agent Windows nie jest uruchomiona, zapobiega to hybrydowy proces roboczy elementu Runbook komunikację z usługi Automatyzacja Azure.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, agent nie działa, wprowadzając następujące polecenie w programie PowerShell: `Get-Service healthservice`. Jeśli usługa zostanie zatrzymana, wprowadź następujące polecenie w programie PowerShell, aby uruchomić usługę: `Start-Service healthservice`.

###<a name="event-4502"></a> Zdarzenia 4502 w Dzienniku programu Operations Manager

#### <a name="issue"></a>Problem

W **aplikacji i usług Menedżera Logs\Operations** dziennika zdarzeń, zobacz zdarzenia 4502 i zawierający EventMessage **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**z następujący opis: *certyfikat przedstawiony przez usługę \<wsid\>. oms.opinsights.azure.com nie został wystawiony przez urząd certyfikacji używany dla usług firmy Microsoft. Skontaktuj się z administratorem sieci, aby sprawdzić, czy działają z serwera proxy przechwytującego komunikację TLS/SSL. Artykuł KB3126513 zawiera dodatkowe informacje dotyczące rozwiązywania problemów, które występują problemy dotyczące połączenia.*

#### <a name="cause"></a>Przyczyna

Może to być spowodowane serwera proxy lub sieci Zapora blokuje komunikację do systemu Microsoft Azure. Sprawdź, czy komputer ma dostęp ruchu wychodzącego do *.azure automation.net na porty 443.

#### <a name="resolution"></a>Rozwiązanie

Dzienniki są przechowywane lokalnie na każdym hybrydowy proces roboczy na C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Można sprawdzić, czy istnieją ostrzeżenia lub błędu zdarzeń zapisywanych w **aplikacji i usług Logs\Microsoft-SMA\Operations** i **aplikacji i usług Menedżera Logs\Operations** dziennika zdarzeń Wskazuje z łącznością lub innego problemu dołączania roli do automatyzacji Azure lub problem podczas wykonywania normalnych operacji.

[Runbook dane wyjściowe i komunikaty](../automation-runbook-output-and-messages.md) są wysyłane do usługi Automatyzacja Azure z hybrydowych procesów roboczych, podobnie jak zadania elementów runbook działają w chmurze. Można również włączyć pełne i postępu strumienie tak samo jak dla innych elementów runbook.

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie została uwzględniona problemu lub nie można rozwiązać problemu, odwiedź jedną z następujących kanałów więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy możesz pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskać obsługuje**.
