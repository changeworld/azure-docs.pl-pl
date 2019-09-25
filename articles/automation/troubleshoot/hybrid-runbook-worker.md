---
title: Rozwiązywanie problemów — Azure Automation hybrydowych procesów roboczych elementów Runbook
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów Azure Automation hybrydowych procesów roboczych elementów Runbook
services: automation
ms.service: automation
ms.subservice: ''
author: bobbytreed
ms.author: robreed
ms.date: 02/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 39cf6126f6212b6e83f1974dae7aaab0038e69c6
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240982"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Rozwiązywanie problemów dotyczących hybrydowych procesów roboczych elementów Runbook

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z hybrydowymi procesami roboczymi elementów Runbook.

## <a name="general"></a>Ogólne

Hybrydowy proces roboczy elementu Runbook zależy od agenta, który komunikuje się z kontem usługi Automation w celu zarejestrowania procesu roboczego, odbierania zadań elementu Runbook i stanu raportu. W przypadku systemu Windows ten Agent jest Microsoft Monitoring Agent. W systemie Linux jest to Agent pakietu OMS dla systemu Linux.

### <a name="runbook-execution-fails"></a>Scenariusz Wykonanie elementu Runbook nie powiodło się

#### <a name="issue"></a>Problem

Wykonanie elementu Runbook nie powiodło się i zostanie wyświetlony następujący błąd:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Element Runbook zostanie wkrótce zawieszony po próbie jego wykonania trzykrotnie. Istnieją warunki, które mogą spowodować przerwanie wykonywania elementu Runbook. W takim przypadku komunikat o błędzie związanym z błędami może nie zawierać żadnych dodatkowych informacji, które informują o tym, dlaczego.

#### <a name="cause"></a>Przyczyna

Oto potencjalne możliwe przyczyny:

* Elementy Runbook nie mogą uwierzytelniać się za pomocą zasobów lokalnych

* Hybrydowy proces roboczy jest za serwerem proxy lub zaporą

* Elementy Runbook nie mogą uwierzytelniać się za pomocą zasobów lokalnych

* Komputer skonfigurowany do uruchamiania funkcji hybrydowego procesu roboczego elementu Runbook nie spełnia minimalnych wymagań sprzętowych.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy komputer ma dostęp wychodzący do *. azure-automation.net na porcie 443.

Komputery z uruchomionym hybrydowym procesem roboczym elementu Runbook powinny spełniać minimalne wymagania sprzętowe, zanim zostaną skonfigurowane do obsługi tej funkcji. Elementy Runbook i procesy w tle, których używają, mogą spowodować nadmierne wykorzystanie systemu i spowodować opóźnienia zadań elementu Runbook lub przekroczenia limitów czasu.

Upewnij się, że komputer, na którym zostanie uruchomiona funkcja hybrydowego procesu roboczego elementu Runbook, spełnia minimalne wymagania sprzętowe. W takim przypadku należy monitorować użycie procesora i pamięci, aby określić korelację między wydajnością hybrydowych procesów roboczych elementów Runbook i systemu Windows. W przypadku wykorzystania pamięci lub użycia procesora CPU może to wskazywać konieczność uaktualnienia zasobów. Możesz również wybrać inny zasób obliczeniowy, który może obsługiwać minimalne wymagania i skalę, gdy wymagania dotyczące obciążenia wskazują, że jest to konieczne.

Sprawdź dziennik zdarzeń **firmy Microsoft dla programu SMA** , aby uzyskać odpowiednie zdarzenie z opisem *procesu Win32, zakończono z kodem [4294967295]* . Przyczyną tego błędu nie jest konfiguracja uwierzytelniania w elementach Runbook ani określone poświadczenia Uruchom jako dla grupy hybrydowych procesów roboczych. Przejrzyj [uprawnienia elementu Runbook](../automation-hrw-run-runbooks.md#runbook-permissions) , aby upewnić się, że uwierzytelnianie dla elementów Runbook zostało prawidłowo skonfigurowane.

### <a name="no-cert-found"></a>Scenariusz Nie znaleziono certyfikatu w magazynie certyfikatów w hybrydowym procesie roboczym elementu Runbook

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

#### <a name="resolution"></a>Rozwiązanie

Jeśli hybrydowy proces roboczy elementu Runbook jest maszyną wirtualną platformy Azure, możesz zamiast tego użyć [zarządzanych tożsamości dla zasobów platformy Azure](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) . Ten scenariusz umożliwia uwierzytelnianie w zasobach platformy Azure przy użyciu tożsamości zarządzanej maszyny wirtualnej platformy Azure zamiast konta Uruchom jako, upraszczając uwierzytelnianie. Gdy hybrydowy proces roboczy elementu Runbook jest maszyną lokalną, należy zainstalować na tym komputerze certyfikat konta Uruchom jako. Aby dowiedzieć się, jak zainstalować certyfikat, zobacz kroki umożliwiające uruchomienie elementu Runbook [Export-RunAsCertificateToHybridWorker](../automation-hrw-run-runbooks.md#runas-script) .

## <a name="linux"></a>Linux

Hybrydowy proces roboczy elementu Runbook systemu Linux zależy od Agent pakietu OMS dla systemu Linux, aby komunikować się z kontem usługi Automation w celu zarejestrowania procesu roboczego, odbierania zadań elementu Runbook i stanu raportu. Jeśli rejestracja procesu roboczego nie powiedzie się, poniżej przedstawiono niektóre możliwe przyczyny błędu:

### <a name="oms-agent-not-running"></a>Scenariusz Agent pakietu OMS dla systemu Linux nie jest uruchomiona

#### <a name="issue"></a>Problem

Agent pakietu OMS dla systemu Linux nie jest uruchomiona

#### <a name="cause"></a>Przyczyna

Jeśli Agent pakietu OMS dla systemu Linux nie jest uruchomiona, uniemożliwia on komunikację hybrydowego procesu roboczego elementu Runbook z systemem Linux z Azure Automation. Agent może nie działać z różnych powodów.

#### <a name="resolution"></a>Rozwiązanie

 Sprawdź, czy Agent jest uruchomiony, wprowadzając następujące polecenie: `ps -ef | grep python`. Powinny zostać wyświetlone dane wyjściowe podobne do następujących: procesy języka Python z kontem użytkownika **nxautomation** . Jeśli Update Management lub rozwiązania Azure Automation nie są włączone, żaden z następujących procesów nie jest uruchomiony.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Poniższa lista zawiera procesy uruchomione dla hybrydowego procesu roboczego elementu Runbook systemu Linux. Są one dostępne w `/var/opt/microsoft/omsagent/state/automationworker/` katalogu.


* **OMS. conf** — ta wartość jest procesem Menedżera procesów roboczych. Jest on uruchamiany bezpośrednio z DSC.

* **Worker. conf** — ten proces jest zarejestrowanego przez proces hybrydowego procesu roboczego, który jest uruchamiany przez Menedżera procesów roboczych. Ten proces jest używany przez Update Management i jest niewidoczny dla użytkownika. Ten proces nie jest obecny, jeśli na komputerze nie jest włączone rozwiązanie Update Management.

* **możesz/Worker. conf** — ten proces to proces hybrydowego procesu roboczego możesz. Proces hybrydowego procesu roboczego możesz służy do wykonywania elementów Runbook użytkownika w hybrydowym procesie roboczym elementu Runbook. Różni się on od automatycznej rejestracji hybrydowego procesu roboczego w kluczowym szczegółzie, który używa innej konfiguracji. Ten proces nie jest obecny, jeśli Azure Automation rozwiązanie jest wyłączone i hybrydowy proces roboczy możesz Linux nie jest zarejestrowany.

Jeśli Agent pakietu OMS dla systemu Linux nie jest uruchomiona, uruchom następujące polecenie, aby uruchomić usługę: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Scenariusz Określona Klasa nie istnieje

Jeśli zostanie wyświetlony błąd: **Określona Klasa nie istnieje...** w tym `/var/opt/microsoft/omsconfig/omsconfig.log` celu należy zaktualizować agent pakietu OMS dla systemu Linux. Uruchom następujące polecenie, aby ponownie zainstalować agenta pakietu OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Hybrydowy proces roboczy elementu Runbook systemu Windows zależy od Microsoft Monitoring Agent, aby komunikować się z kontem usługi Automation w celu zarejestrowania procesu roboczego, odbierania zadań elementu Runbook i stanu raportu. Jeśli rejestracja procesu roboczego nie powiedzie się, poniżej przedstawiono niektóre możliwe przyczyny błędu:

### <a name="mma-not-running"></a>Scenariusz Microsoft Monitoring Agent nie jest uruchomiona

#### <a name="issue"></a>Problem

`healthservice` Usługa nie jest uruchomiona na maszynie hybrydowej procesu roboczego elementu Runbook.

#### <a name="cause"></a>Przyczyna

Jeśli usługa Microsoft Monitoring Agent systemu Windows nie jest uruchomiona, ten stan uniemożliwia komunikację hybrydowego procesu roboczego elementu Runbook z Azure Automation.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy Agent jest uruchomiony, wprowadzając następujące polecenie w programie PowerShell `Get-Service healthservice`:. Jeśli usługa jest zatrzymana, wprowadź następujące polecenie w programie PowerShell, aby uruchomić usługę: `Start-Service healthservice`.

### <a name="event-4502"></a>Zdarzenie 4502 w dzienniku Operations Manager

#### <a name="issue"></a>Problem

W dzienniku zdarzeń programu **Application and Services Logs\Operations Manager** zobaczysz zdarzenie 4502 i EventMessage zawierające **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent** z następującym opisem: *Certyfikat przedstawiony przez usługę \<WSID\>. OMS.OpInsights.Azure.com nie został wystawiony przez urząd certyfikacji używany przez usługi firmy Microsoft. Skontaktuj się z administratorem sieci, aby sprawdzić, czy jest uruchomiony serwer proxy, który przechwytuje komunikację TLS/SSL. Artykuł KB3126513 zawiera dodatkowe informacje dotyczące rozwiązywania problemów z łącznością.*

#### <a name="cause"></a>Przyczyna

Przyczyną tego problemu może być Microsoft Azure przez serwer proxy lub zaporę sieciową. Sprawdź, czy komputer ma dostęp wychodzący do *. azure-automation.net na portach 443.

#### <a name="resolution"></a>Rozwiązanie

Dzienniki są przechowywane lokalnie na każdym hybrydowym procesie roboczym w witrynie C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Możesz sprawdzić, czy w dzienniku zdarzeń **aplikacji i usług Logs\Microsoft-SMA\Operations** i **aplikacji i usług Logs\Operations Manager** znajdują się jakieś zdarzenia ostrzegawcze, które wskazują łączność lub inny problem, który ma wpływ na dołączanie roli do Azure Automation lub problemów w ramach normalnych operacji.

[Dane wyjściowe i komunikaty elementu Runbook](../automation-runbook-output-and-messages.md) są wysyłane do Azure Automation od hybrydowych procesów roboczych, podobnie jak zadania elementu Runbook działające w chmurze. Możesz również włączyć strumienie pełnych i postępu w taki sam sposób, jak w przypadku innych elementów Runbook.

### <a name="corrupt-cache"></a>Hybrydowy proces roboczy elementu Runbook nie jest zgłaszany

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

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, zaloguj się do hybrydowego procesu roboczego elementu Runbook i uruchom poniższy skrypt. Ten skrypt powoduje zatrzymanie Microsoft Monitoring Agent, usunięcie jego pamięci podręcznej i ponowne uruchomienie usługi. Ta akcja Wymusza ponowne pobranie konfiguracji przez proces hybrydowego elementu Runbook programu z Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>Scenariusz Nie można dodać hybrydowego procesu roboczego elementu Runbook

#### <a name="issue"></a>Problem

Podczas próby dodania hybrydowego procesu roboczego elementu Runbook za pomocą `Add-HybridRunbookWorker` polecenia cmdlet zostanie wyświetlony następujący komunikat.

```error
Machine is already registered
```

#### <a name="cause"></a>Przyczyna

Może to być spowodowane tym, że maszyna jest już zarejestrowana przy użyciu innego konta usługi Automation lub jeśli spróbujesz ponownie dodać hybrydowy proces roboczy elementu Runbook po usunięciu go z komputera.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, usuń następujący klucz rejestru, a `HealthService` `Add-HybridRunbookWorker` następnie ponownie uruchom polecenie cmdlet:

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.

