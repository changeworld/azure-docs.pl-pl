---
title: Rozwiązywanie problemów — usługa Azure Automation hybrydowych procesów roboczych Runbook
description: Ten artykuł zawiera informacje, rozwiązywanie problemów z usługi Azure Automation hybrydowych — procesów roboczych Runbook
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a95c9f1edd6983c915316f2900885a8131245860
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437837"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Rozwiązywanie problemów z hybrydowych procesów roboczych Runbook

Ten artykuł zawiera informacje na temat rozwiązywania problemów za pomocą hybrydowych procesów roboczych Runbook.

## <a name="general"></a>Ogólne

Hybrydowy proces roboczy elementu Runbook zależy od agenta do komunikowania się z konta usługi Automation do rejestrowania procesu roboczego i raportów o stanie oraz odbieranie zadań elementów runbook. Dla Windows ten agent jest program Microsoft Monitoring Agent. W przypadku systemu Linux jest agenta pakietu OMS dla systemu Linux.

### <a name="runbook-execution-fails"></a>Scenariusz: Wykonanie elementu Runbook nie powiodło się.

#### <a name="issue"></a>Problem

Wykonanie elementu Runbook nie powiedzie się i zostanie wyświetlony następujący błąd:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Element runbook jest zawieszona wkrótce po próby wykonania jej trzy razy. Istnieją warunki, które mogą przerwać pomyślne zakończenie działania elementu runbook i powiązane komunikat nie zawiera żadnych dodatkowych informacji, która wskazuje, dlaczego.

#### <a name="cause"></a>Przyczyna

Poniżej przedstawiono potencjalne przyczyny:

* Elementy runbook nie mogą uwierzytelnić się przy użyciu zasobów lokalnych

* Hybrydowy proces roboczy jest używany serwer proxy lub Zapora

* Elementy runbook nie mogą uwierzytelnić się przy użyciu zasobów lokalnych

* Komputer wybrany do obsługiwania funkcji hybrydowego procesu roboczego Runbook spełnia minimalne wymagania sprzętowe.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy komputer ma dostęp ruchu wychodzącego do *.azure-automation.net na porcie 443.

Komputery z systemem hybrydowego procesu roboczego Runbook powinny spełniać minimalne wymagania sprzętowe przed wyznaczając ją do obsługi tej funkcji. W przeciwnym razie w zależności od wykorzystania zasobów innych procesów w tle i rywalizacji o nie podczas umożliwia wykonanie elementu runbook Przyczyna komputera staje się nadmiernie i powodować opóźnienia zadania elementu runbook lub przekroczenia limitu czasu.

Upewnij się, że komputer wybrany do obsługiwania funkcji hybrydowego procesu roboczego Runbook spełnia minimalne wymagania sprzętowe. Jeśli tak jest, należy monitorować wykorzystanie Procesora i pamięci, aby określić, wszelka korelacja między wydajności procesów hybrydowego procesu roboczego Runbook i Windows. Jeśli istnieje pamięci lub dużego wykorzystania procesora CPU, może to oznaczać konieczność, aby uaktualnić lub dodać dodatkowych procesorów lub zwiększ ilość pamięci eliminują wąskie gardło zasobów i naprawić błąd. Opcjonalnie zaznacz zasób obliczeniowej, który może obsługiwać minimalne wymagania i skalowania, gdy obciążenia wskazują, że wzrost jest niezbędne.

Sprawdź **Microsoft SMA** dziennik zdarzeń pod kątem odpowiednie zdarzenie z opisem *Win32 proces został zakończony z kodem [4294967295]*. Przyczyną tego błędu jest nie jeszcze skonfigurowane uwierzytelnianie w elementach runbook lub podania poświadczeń Uruchom jako dla grupy hybrydowych procesów roboczych. Przegląd [uprawnienia elementu Runbook](../automation-hrw-run-runbooks.md#runbook-permissions) aby upewnić się, prawidłowo skonfigurowano uwierzytelniania dla elementów runbook.

## <a name="linux"></a>Linux

Linux hybrydowego procesu roboczego Runbook zależy od agenta pakietu OMS dla systemu Linux do komunikowania się z kontem usługi Automation do rejestrowania procesu roboczego i raportów o stanie oraz odbieranie zadań elementów runbook. W przypadku niepowodzenia rejestracji procesu roboczego, poniżej przedstawiono niektóre możliwe przyczyny błędu:

### <a name="oms-agent-not-running"></a>Scenariusz: Nie uruchomiono agenta pakietu OMS dla systemu Linux

Jeśli nie jest uruchomiony Agent pakietu OMS dla systemu Linux, zapobiega to Linux hybrydowego procesu roboczego Runbook podczas komunikowania się z usługą Azure Automation. Sprawdź agent jest uruchomiony, wprowadzając następujące polecenie: `ps -ef | grep python`. Powinien zostać wyświetlony dane wyjściowe podobne do następujących procesów python przy użyciu **nxautomation** konta użytkownika. Jeśli rozwiązania Update Management lub usługi Azure Automation nie są włączone, żaden z następujących procesów uruchomionych.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Na poniższej liście przedstawiono procesy, które są uruchamiane dla procesu roboczego elementu Runbook dla hybrydowych w systemie Linux. Znajdują się one wszystkie w `/var/opt/microsoft/omsagent/state/automationworker/` katalogu.

* **OMS.conf** — ten proces jest proces menedżera procesów roboczych, ten proces jest uruchomiony bezpośrednio z DSC.

* **Worker.conf** — ten proces jest automatycznie rejestrowane hybrydowego procesu roboczego, jest uruchomiona przez menedżera procesów roboczych. Ten proces jest używany przez rozwiązanie Update Management i jest niewidoczne dla użytkownika. Ten proces nie jest wyświetlany, jeśli rozwiązanie Update Management nie jest włączona na komputerze.

* **diy/Worker.conf** — ten proces jest możesz hybrydowego procesu roboczego. Możesz hybrydowy proces roboczy jest używany do wykonania użytkownika elementów runbook w hybrydowym procesie roboczym elementu Runbook. Tylko różni się od automatycznego zarejestrowany hybrydowego procesu roboczego szczegółowo kluczy, które jest używane z innej konfiguracji. Ten proces nie jest obecny, jeśli rozwiązanie usługi Azure Automation nie jest włączone, i możesz hybrydowego procesu roboczego systemu Linux nie jest zarejestrowany.

Jeśli Agent pakietu OMS dla systemu Linux nie jest uruchomiona, uruchom następujące polecenie, aby uruchomić usługę: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Scenariusz: Określona klasa nie istnieje.

Jeśli zostanie wyświetlony błąd: **Nie ma określonej klasy...** w `/var/opt/microsoft/omsconfig/omsconfig.log` , a następnie agenta pakietu OMS dla systemu Linux, musi zostać zaktualizowany. Uruchom następujące polecenie, aby ponownie zainstalować agenta pakietu OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows hybrydowego procesu roboczego Runbook zależy od Microsoft Monitoring Agent mógł komunikować się z konta usługi Automation do rejestrowania procesu roboczego i raportów o stanie oraz odbieranie zadań elementów runbook. W przypadku niepowodzenia rejestracji procesu roboczego, poniżej przedstawiono niektóre możliwe przyczyny błędu:

### <a name="mma-not-running"></a>Scenariusz: Program Microsoft Monitoring Agent nie jest uruchomiona.

#### <a name="issue"></a>Problem

`healthservice` Usługa nie jest uruchomiona na maszynie hybrydowego procesu roboczego Runbook.

#### <a name="cause"></a>Przyczyna

Jeśli usługa Microsoft Monitoring Agent Windows nie jest uruchomiona, w tym scenariuszu uniemożliwia komunikację z usługą Azure Automation hybrydowy proces roboczy elementu Runbook.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź agent jest uruchomiony, wprowadzając następujące polecenie w programie PowerShell: `Get-Service healthservice`. Jeśli usługa zostanie zatrzymana, wprowadź następujące polecenie w programie PowerShell, aby uruchomić usługę: `Start-Service healthservice`.

### <a name="event-4502"></a> Zdarzenia 4502 w Dzienniku programu Operations Manager

#### <a name="issue"></a>Problem

W **Application and Services log\operations Manager** dziennika zdarzeń, zobaczysz zdarzenia 4502 i EventMessage, który zawiera **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**z poniższym opisem: *Certyfikat przedstawiony przez usługę \<wsid\>. oms.opinsights.azure.com nie został wystawiony przez urząd certyfikacji używany dla usług firmy Microsoft. Skontaktuj się z administratorem sieci, aby sprawdzić, czy działają z serwera proxy przechwytującego komunikację TLS/SSL. Artykuł KB3126513 zawiera dodatkowe informacje dotyczące rozwiązywania problemów dotyczące problemów z łącznością.*

#### <a name="cause"></a>Przyczyna

Ten problem może być spowodowane serwera proxy lub sieci Zapora blokuje komunikację w systemie Microsoft Azure. Sprawdź, czy komputer ma dostęp ruchu wychodzącego do *.azure-automation.net porty 443.

#### <a name="resolution"></a>Rozwiązanie

Dzienniki są przechowywane lokalnie na poszczególnych hybrydowy proces roboczy w C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Możesz sprawdzić, czy wszelkie ostrzeżenia lub błędu zdarzenia w **aplikacji i usług Logs\Microsoft-SMA\Operations** i **Application and Services log\operations Manager** dziennika zdarzeń, które miałyby Wskazuje, z łącznością lub inny problem, który ma wpływ na dołączanie do roli w usłudze Azure Automation lub problem w ramach normalnych operacji.

[Element Runbook dane wyjściowe i komunikaty](../automation-runbook-output-and-messages.md) są wysyłane do usługi Azure Automation z hybrydowych procesów roboczych, tak samo, jak zadań elementów runbook, które działają w chmurze. Można również włączyć strumienie pełne informacje i postęp taki sam sposób jak w przypadku innych elementach runbook.

### <a name="corrupt-cache"></a> Hybrydowy proces roboczy elementu Runbook nie zgłaszają

#### <a name="issue"></a>Problem

Komputer hybrydowego procesu roboczego Runbook działa, ale nie widzisz danych pulsu dla komputera w obszarze roboczym.

Poniższe przykładowe zapytanie pokazuje maszyny w obszarze roboczym i ich ostatniego pulsu:

```loganalytics
// Last heartbeat of each computer
Heartbeat 
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Przyczyna

Ten problem może być spowodowane w uszkodzona pamięć podręczna w hybrydowym procesie roboczym elementu Runbook.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, zaloguj się w hybrydowym procesie roboczym elementu Runbook, a następnie uruchom następujący skrypt. Ten skrypt zatrzymuje program Microsoft Monitoring Agent, usunięcie pamięci podręcznej i ponowne uruchomienie usługi. Ta akcja powoduje hybrydowy proces roboczy elementu Runbook można ponownie pobrać swojej konfiguracji z usługi Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie był widoczny problemu lub są w stanie rozwiązać problemu, odwiedź jedną z następujących kanałów obsługi więcej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.
