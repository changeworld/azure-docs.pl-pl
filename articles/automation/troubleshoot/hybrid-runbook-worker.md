---
title: Rozwiązywanie problemów — Azure Automation hybrydowych procesów roboczych elementów Runbook
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów Azure Automation hybrydowych procesów roboczych elementów Runbook
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a5885df67464095061d9a95aa59010a1629fb8f8
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76930344"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Rozwiązywanie problemów dotyczących hybrydowych procesów roboczych elementów Runbook

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z hybrydowymi procesami roboczymi elementów Runbook.

## <a name="general"></a>Ogólne

Hybrydowy proces roboczy elementu Runbook zależy od agenta, który komunikuje się z kontem usługi Automation w celu zarejestrowania procesu roboczego, odbierania zadań elementu Runbook i stanu raportu. Dla systemu Windows ten Agent jest agentem Log Analytics dla systemu Windows (określanym również jako Microsoft Monitoring Agent (MMA)). W przypadku systemu Linux jest to Agent Log Analytics dla systemu Linux.

### <a name="runbook-execution-fails"></a>Scenariusz: wykonanie elementu Runbook nie powiodło się

#### <a name="issue"></a>Problem

Wykonanie elementu Runbook nie powiodło się i zostanie wyświetlony następujący błąd:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Element Runbook zostanie wkrótce zawieszony po próbie jego wykonania trzykrotnie. Istnieją warunki, które mogą przerwać zakończenie działania elementu Runbook. Komunikat o błędzie pokrewny może nie zawierać żadnych dodatkowych informacji.

#### <a name="cause"></a>Przyczyna

Oto potencjalne możliwe przyczyny:

* Elementy Runbook nie mogą uwierzytelniać się za pomocą zasobów lokalnych

* Hybrydowy proces roboczy jest za serwerem proxy lub zaporą

* Elementy Runbook nie mogą uwierzytelniać się za pomocą zasobów lokalnych

* Komputer skonfigurowany do uruchamiania funkcji hybrydowego procesu roboczego elementu Runbook nie spełnia minimalnych wymagań sprzętowych.

#### <a name="resolution"></a>Rozdzielczość

Sprawdź, czy komputer ma dostęp wychodzący do *. azure-automation.net na porcie 443.

Komputery z uruchomionym hybrydowym procesem roboczym elementu Runbook powinny spełniać minimalne wymagania sprzętowe, zanim proces roboczy zostanie skonfigurowany do obsługi tej funkcji. Elementy Runbook i procesy w tle, których używają, mogą spowodować nadmierne użycie systemu i spowodować opóźnienia zadań elementu Runbook lub przekroczenia limitów czasu.

Upewnij się, że komputer, na którym zostanie uruchomiona funkcja hybrydowego procesu roboczego elementu Runbook, spełnia minimalne wymagania sprzętowe. W takim przypadku należy monitorować użycie procesora i pamięci, aby określić korelację między wydajnością hybrydowych procesów roboczych elementów Runbook i systemu Windows. Każda pamięć lub wykorzystanie procesora CPU może wskazywać potrzebę uaktualnienia zasobów. Możesz również wybrać inny zasób obliczeniowy, który może obsługiwać minimalne wymagania i skalę, gdy wymagania dotyczące obciążenia wskazują, że jest to konieczne.

Sprawdź dziennik zdarzeń **firmy Microsoft dla programu SMA** , aby uzyskać odpowiednie zdarzenie z opisem *procesu Win32, zakończono z kodem [4294967295]* . Przyczyną tego błędu nie jest konfiguracja uwierzytelniania w elementach Runbook ani określone poświadczenia Uruchom jako dla grupy hybrydowych procesów roboczych. Przejrzyj [uprawnienia elementu Runbook](../automation-hrw-run-runbooks.md#runbook-permissions) , aby upewnić się, że uwierzytelnianie dla elementów Runbook zostało prawidłowo skonfigurowane.

### <a name="no-cert-found"></a>Scenariusz: nie znaleziono certyfikatu w magazynie certyfikatów w hybrydowym procesie roboczym elementu Runbook

#### <a name="issue"></a>Problem

Element Runbook uruchomiony w hybrydowym procesie roboczym elementu Runbook kończy się niepowodzeniem z następującym komunikatem o błędzie:

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas próby użycia [konta Uruchom jako](../manage-runas-account.md) w elemencie Runbook, który jest uruchamiany w hybrydowym procesie roboczym elementu Runbook, gdzie nie istnieje certyfikat konta Uruchom jako. Hybrydowe procesy robocze elementów Runbook nie mają domyślnie elementu zawartości certyfikatu lokalnego, co jest wymagane przez konto Uruchom jako do prawidłowego działania.

#### <a name="resolution"></a>Rozdzielczość

Jeśli hybrydowy proces roboczy elementu Runbook jest maszyną wirtualną platformy Azure, możesz zamiast tego użyć [zarządzanych tożsamości dla zasobów platformy Azure](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) . Ten scenariusz upraszcza uwierzytelnianie, umożliwiając uwierzytelnianie w zasobach platformy Azure przy użyciu tożsamości zarządzanej maszyny wirtualnej platformy Azure zamiast konta Uruchom jako. Gdy hybrydowy proces roboczy elementu Runbook jest maszyną lokalną, należy zainstalować na tym komputerze certyfikat konta Uruchom jako. Aby dowiedzieć się, jak zainstalować certyfikat, zobacz procedurę uruchamiania programu PowerShell Runbook Export-RunAsCertificateToHybridWorker w [uruchomionych elementach Runbook w hybrydowym procesie roboczym elementu Runbook](../automation-hrw-run-runbooks.md).

## <a name="linux"></a>Linux

Hybrydowy proces roboczy elementu Runbook systemu Linux zależy od [agenta log Analytics dla systemu Linux](../../azure-monitor/platform/log-analytics-agent.md) do komunikowania się z kontem usługi Automation w celu zarejestrowania procesu roboczego, odebrania zadań elementu Runbook i stanu raportu. Jeśli rejestracja procesu roboczego nie powiedzie się, poniżej przedstawiono niektóre możliwe przyczyny błędu:

### <a name="oms-agent-not-running"></a>Scenariusz: Agent Log Analytics dla systemu Linux nie jest uruchomiony

#### <a name="issue"></a>Problem

Agent Log Analytics dla systemu Linux nie jest uruchomiony

#### <a name="cause"></a>Przyczyna

Jeśli Agent nie jest uruchomiony, uniemożliwia komunikację hybrydowej elementu Runbook systemu Linux z Azure Automation. Agent może nie działać z różnych powodów.

#### <a name="resolution"></a>Rozdzielczość

 Sprawdź, czy Agent jest uruchomiony, wprowadzając następujące polecenie: `ps -ef | grep python`. Powinny zostać wyświetlone dane wyjściowe podobne do następujących: procesy języka Python z kontem użytkownika **nxautomation** . Jeśli Update Management lub rozwiązania Azure Automation nie są włączone, żaden z następujących procesów nie jest uruchomiony.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Poniższa lista zawiera procesy uruchomione dla hybrydowego procesu roboczego elementu Runbook systemu Linux. Są one dostępne w katalogu `/var/opt/microsoft/omsagent/state/automationworker/`.


* **OMS. conf** — ta wartość jest procesem Menedżera procesów roboczych. Jest on uruchamiany bezpośrednio z DSC.

* **Worker. conf** — ten proces jest zarejestrowanego przez proces hybrydowego procesu roboczego, który jest uruchamiany przez Menedżera procesów roboczych. Ten proces jest używany przez Update Management i jest niewidoczny dla użytkownika. Ten proces nie jest obecny, jeśli na komputerze nie jest włączone rozwiązanie Update Management.

* **możesz/Worker. conf** — ten proces to proces hybrydowego procesu roboczego możesz. Proces hybrydowego procesu roboczego możesz służy do wykonywania elementów Runbook użytkownika w hybrydowym procesie roboczym elementu Runbook. Różni się on od automatycznej rejestracji hybrydowego procesu roboczego w kluczowym szczegółowo, że używa innej konfiguracji. Ten proces nie występuje, jeśli Azure Automation rozwiązanie jest wyłączone i nie zarejestrowano hybrydowego procesu roboczego możesz Linux.

Jeśli Agent nie jest uruchomiony, uruchom następujące polecenie, aby uruchomić usługę: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="error-403-on-registration"></a>Scenariusz: Błąd 403 podczas rejestrowania hybrydowego procesu roboczego elementu Runbook

#### <a name="issue"></a>Problem

Początkowa faza rejestracji procesu roboczego kończy się niepowodzeniem i pojawia się następujący błąd (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Przyczyna

Możliwe są następujące przyczyny:
* Istnieje nieprawidłowy typ identyfikatora obszaru roboczego lub klucza obszaru roboczego (podstawowego) w ustawieniach agenta. 
* Hybrydowy proces roboczy elementu Runbook nie może pobrać konfiguracji, powodując błąd łączenia konta. Gdy platforma Azure umożliwia korzystanie z rozwiązań, obsługuje tylko niektóre regiony do łączenia obszaru roboczego Log Analytics i konta usługi Automation. Istnieje również możliwość, że na komputerze jest ustawiona nieprawidłowa data i/lub godzina. Jeśli czas wynosi +/-15 minut od bieżącego czasu, dołączanie kończy się niepowodzeniem.

#### <a name="resolution"></a>Rozdzielczość

##### <a name="mistyped-workspace-idkey"></a>Niewpisany identyfikator/klucz obszaru roboczego
Aby sprawdzić, czy identyfikator obszaru roboczego agenta lub klucz obszaru roboczego został wpisany w sposób nieprawidłowy, zobacz [Dodawanie lub usuwanie obszaru roboczego — Agent systemu](../../azure-monitor/platform/agent-manage.md#windows-agent) Windows dla agenta systemu Windows lub [Dodawanie lub usuwanie agenta obszaru roboczego — Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) dla agenta Linux.  Upewnij się, że wybrano pełny ciąg z Azure Portal i skopiuj go i wklej uważnie.

##### <a name="configuration-not-downloaded"></a>Nie pobrano konfiguracji

Obszar roboczy Log Analytics i konto usługi Automation muszą znajdować się w połączonym regionie. Listę obsługiwanych regionów można znaleźć w temacie [Azure Automation i log Analytics mapowania obszaru roboczego](../how-to/region-mappings.md).

Może być również konieczne zaktualizowanie daty i/lub strefy czasowej komputera. W przypadku wybrania niestandardowego zakresu czasu upewnij się, że zakres jest w formacie UTC, który może się różnić od lokalnej strefy czasowej.

### <a name="class-does-not-exist"></a>Scenariusz: określona Klasa nie istnieje

Jeśli zobaczysz błąd **, określona Klasa nie istnieje...** w `/var/opt/microsoft/omsconfig/omsconfig.log`należy zaktualizować agenta Log Analytics dla systemu Linux. Uruchom następujące polecenie, aby ponownie zainstalować agenta:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Hybrydowy proces roboczy elementu Runbook systemu Windows zależy od [agenta log Analytics dla systemu Windows](../../azure-monitor/platform/log-analytics-agent.md) , który komunikuje się z kontem usługi Automation w celu zarejestrowania procesu roboczego, odbierania zadań elementu Runbook i stanu raportu. Jeśli rejestracja procesu roboczego nie powiedzie się, poniżej przedstawiono niektóre możliwe przyczyny błędu:

### <a name="mma-not-running"></a>Scenariusz: nie uruchomiono Microsoft Monitoring Agent

#### <a name="issue"></a>Problem

Usługa `healthservice` nie jest uruchomiona na maszynie hybrydowej elementu Runbook Worker.

#### <a name="cause"></a>Przyczyna

Jeśli Microsoft Monitoring Agent usługa firmy Microsoft nie jest uruchomiona, ten stan uniemożliwia komunikację hybrydowego procesu roboczego elementu Runbook z Azure Automation.

#### <a name="resolution"></a>Rozdzielczość

Sprawdź, czy Agent jest uruchomiony, wprowadzając następujące polecenie w programie PowerShell: `Get-Service healthservice`. Jeśli usługa jest zatrzymana, wprowadź następujące polecenie w programie PowerShell, aby uruchomić usługę: `Start-Service healthservice`.

### <a name="event-4502"></a>Scenariusz: zdarzenie 4502 w dzienniku Operations Manager

#### <a name="issue"></a>Problem

W dzienniku zdarzeń programu **Application and Services Logs\Operations Manager** zobaczysz zdarzenie 4502 i EventMessage, które zawiera **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent** z następującym opisem: *certyfikat przedstawiony przez usługę \<WSID\>. OMS.OpInsights.Azure.com nie został wystawiony przez urząd certyfikacji używany przez usługi firmy Microsoft. Skontaktuj się z administratorem sieci, aby sprawdzić, czy jest uruchomiony serwer proxy, który przechwytuje komunikację TLS/SSL.*

#### <a name="cause"></a>Przyczyna

Przyczyną tego problemu może być Microsoft Azure przez serwer proxy lub zaporę sieciową. Sprawdź, czy komputer ma dostęp wychodzący do *. azure-automation.net na portach 443. 

#### <a name="resolution"></a>Rozdzielczość

Dzienniki są przechowywane lokalnie na każdym hybrydowym procesie roboczym w witrynie C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Możesz sprawdzić, czy w dzienniku zdarzeń **aplikacji i usług Logs\Microsoft-SMA\Operations** i **Application and Services Logs\Operations Manager** znajdują się jakieś zdarzenia ostrzegawcze lub dotyczące błędów, które wskazują na to, czy problem ma wpływ na dołączanie roli do Azure Automation lub problemów w ramach normalnych operacji. Aby uzyskać dodatkową pomoc w rozwiązywaniu problemów z agentem Log Analytics, zobacz [Rozwiązywanie problemów z log Analytics agentem systemu Windows](../../azure-monitor/platform/agent-windows-troubleshoot.md).

[Dane wyjściowe i komunikaty elementu Runbook](../automation-runbook-output-and-messages.md) są wysyłane do Azure Automation od hybrydowych procesów roboczych, podobnie jak zadania elementu Runbook działające w chmurze. Możesz również włączyć strumienie pełnych i postępu w taki sam sposób, jak w przypadku innych elementów Runbook.

### <a name="corrupt-cache"></a>Scenariusz: hybrydowy proces roboczy elementu Runbook nie jest zgłaszany

#### <a name="issue"></a>Problem

Maszyna hybrydowego procesu roboczego elementu Runbook jest uruchomiona, ale nie widzisz danych pulsu dla maszyny w obszarze roboczym.

Poniższe przykładowe zapytanie pokazuje maszyny w obszarze roboczym i ich ostatni puls:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Przyczyna

Przyczyną tego problemu może być uszkodzona pamięć podręczna w hybrydowym procesie roboczym elementu Runbook.

#### <a name="resolution"></a>Rozdzielczość

Aby rozwiązać ten problem, zaloguj się do hybrydowego procesu roboczego elementu Runbook i uruchom poniższy skrypt. Ten skrypt powoduje zatrzymanie Microsoft Monitoring Agent, usunięcie jego pamięci podręcznej i ponowne uruchomienie usługi. Ta akcja powoduje, że hybrydowy proces roboczy elementu Runbook pobierze swoją konfigurację z Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>Scenariusz: nie można dodać hybrydowego procesu roboczego elementu Runbook

#### <a name="issue"></a>Problem

Podczas próby dodania hybrydowego procesu roboczego elementu Runbook za pomocą polecenia cmdlet `Add-HybridRunbookWorker` zostanie wyświetlony następujący komunikat.

```error
Machine is already registered
```

#### <a name="cause"></a>Przyczyna

Ten problem może być spowodowany tym, że maszyna jest już zarejestrowana przy użyciu innego konta usługi Automation lub gdy użytkownik próbuje odczytać hybrydowy proces roboczy elementu Runbook po usunięciu go z komputera.

#### <a name="resolution"></a>Rozdzielczość

Aby rozwiązać ten problem, usuń następujący klucz rejestru i ponownie uruchom `HealthService` i spróbuj ponownie wykonać `Add-HybridRunbookWorker` polecenie cmdlet:

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.