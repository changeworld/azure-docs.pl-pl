---
title: Rozwiązywanie problemów z Update Management
description: Dowiedz się, jak rozwiązywać problemy z Update Management
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 89c5c849ffdbe70ae449f41ac08471c84f2fb5bc
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372629"
---
# <a name="troubleshooting-issues-with-update-management"></a>Rozwiązywanie problemów z Update Management

W tym artykule omówiono rozwiązania rozwiązywania problemów, które mogą być wykonywane w przypadku korzystania z Update Management.

Istnieje narzędzie do rozwiązywania problemów z agentem dla agenta hybrydowego procesu roboczego w celu ustalenia podstawowego problemu. Aby dowiedzieć się więcej na temat narzędzia do rozwiązywania problemów, zobacz [Rozwiązywanie problemów z aktualizowaniem agentów](update-agent-issues.md). Wszystkie inne problemy znajdują się poniżej w szczegółowych informacjach o możliwych problemach.

Jeśli wystąpią problemy podczas próby dołączenia rozwiązania na maszynie wirtualnej, sprawdź dziennik zdarzeń **Operations Manager** w obszarze **Dzienniki aplikacji i usług** na komputerze lokalnym, aby uzyskać zdarzenia z identyfikatorem zdarzenia **4502** i komunikatem o zdarzeniu zawierający **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent**.

W poniższej sekcji przedstawiono określone komunikaty o błędach i możliwe rozwiązanie dla każdego z nich. Aby poznać inne problemy z dołączaniem, zobacz temat [Rozwiązywanie problemów z](onboarding.md)dołączaniem rozwiązania.

## <a name="nologs"></a>Scenariusz: maszyny nie są wyświetlane w portalu w obszarze Update Management

### <a name="issue"></a>Problem

Może działać w następujących scenariuszach:

* Komputer nie jest **skonfigurowany** na podstawie Update Management widoku maszyny wirtualnej

* Brak Twoich maszyn w widoku Update Management konta usługi Automation

* Masz maszyny, które pokazują, że **nie oceniono** pod **względem zgodności**, ale widzisz dane pulsu w dziennikach Azure monitor dla hybrydowego procesu roboczego elementu Runbook, ale nie Update Management.

### <a name="cause"></a>Przyczyna

Może to być spowodowane potencjalnymi problemami z konfiguracją lokalną lub nieprawidłowo skonfigurowanym konfiguracją zakresu.

Może zajść konieczność ponownego zarejestrowania hybrydowego procesu roboczego elementu Runbook i ponownej instalacji.

W obszarze roboczym możesz zdefiniować przydział, który został osiągnięty i zatrzymał przechowywanie danych.

### <a name="resolution"></a>Rozdzielczość

* Uruchom narzędzie do rozwiązywania problemów dla [systemu Windows](update-agent-issues.md#troubleshoot-offline) lub [Linux](update-agent-issues-linux.md#troubleshoot-offline) w zależności od systemu operacyjnego.

* Upewnij się, że komputer jest raportowany do prawidłowego obszaru roboczego. Sprawdź, w jakim obszarze roboczym jest raportowany komputer. Aby uzyskać instrukcje dotyczące sposobu weryfikacji, zobacz [Weryfikowanie łączności agenta z log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Następnie upewnij się, że jest to obszar roboczy, który jest połączony z Twoim kontem Azure Automation. Aby to potwierdzić, przejdź do konta usługi Automation, a następnie kliknij pozycję **połączony obszar roboczy** w obszarze **powiązane zasoby**.

* Zaznacz, aby upewnić się, że maszyny są widoczne w obszarze roboczym Log Analytics. Uruchom następujące zapytanie w obszarze roboczym Log Analytics, który jest połączony z kontem usługi Automation. Jeśli komputer nie jest wyświetlany w wynikach zapytania, komputer nie został ostatnio zaewidencjonowany, co oznacza, że występuje najprawdopodobniej lokalny problem z konfiguracją i można [ponownie zainstalować agenta](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Jeśli komputer jest wyświetlany w wynikach zapytania, należy określić konfigurację zakresu określoną w następującym punkcie.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* Sprawdź, czy występują problemy z konfiguracją zakresu. [Konfiguracja zakresu](../automation-onboard-solutions-from-automation-account.md#scope-configuration) określa, które maszyny mają zostać skonfigurowane dla rozwiązania. Jeśli Twoja maszyna jest wyświetlana w obszarze roboczym, ale nie jest wyświetlana w portalu **Update Management** , musisz skonfigurować konfigurację zakresu, aby docelowa maszynom. Aby dowiedzieć się, jak to zrobić, zobacz sekcję dołączanie [maszyn w obszarze roboczym](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* W obszarze roboczym Uruchom następujące zapytanie. Jeśli zobaczysz wynik `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` masz limit przydziału zdefiniowany w Twoim obszarze roboczym, który został osiągnięty i zatrzymano zapisywanie danych. W obszarze roboczym przejdź do obszaru **użycie i szacowane koszty** > **zarządzania ilością danych** i sprawdź swój limit przydziału lub Usuń posiadany limit przydziału.

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

* Jeśli powyższe kroki nie rozwiążą problemu, wykonaj kroki opisane w sekcji [wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows](../automation-windows-hrw-install.md) w celu ponownego zainstalowania hybrydowego procesu roboczego dla systemu Windows lub [wdrożenia hybrydowego procesu roboczego elementu Runbook](../automation-linux-hrw-install.md) w systemie Linux.

## <a name="rp-register"></a>Scenariusz: nie można zarejestrować dostawcy zasobów usługi Automation dla subskrypcji

### <a name="issue"></a>Problem

Podczas pracy z rozwiązaniami na koncie usługi Automation może zostać wyświetlony następujący błąd.

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Przyczyna

Dostawca zasobów usługi Automation nie jest zarejestrowany w subskrypcji.

### <a name="resolution"></a>Rozdzielczość

Dostawców zasobów usługi Automation można zarejestrować, wykonując następujące kroki w Azure Portal:

1. Kliknij pozycję **wszystkie usługi** na dolnej liście usług platformy Azure, a następnie wybierz pozycję **subskrypcje** w grupie usługi _Ogólne_ .
2. Wybierz subskrypcję.
3. Kliknij pozycję **dostawcy zasobów** w obszarze _Ustawienia_.
4. Z listy dostawców zasobów Sprawdź, czy jest zarejestrowany dostawca zasobów **Microsoft. Automation** .
5. Jeśli dostawca nie znajduje się na liście, zarejestruj dostawcę **Microsoft. Automation** , wykonując czynności opisane w [ ](/azure/azure-resource-manager/resource-manager-register-provider-errors)sekcji.

## <a name="components-enabled-not-working"></a>Scenariusz: składniki dla rozwiązania "Update Management" zostały włączone i teraz ta maszyna wirtualna jest konfigurowana

### <a name="issue"></a>Problem

Po 15 minutach po dołączeniu do maszyny wirtualnej nadal zobaczysz następujący komunikat:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Przyczyna

Przyczyną tego błędu mogą być następujące przyczyny:

1. Trwa blokowanie komunikacji z powrotem do konta usługi Automation.
2. Dołączana maszyna wirtualna może pochodzić ze sklonowanego komputera, który nie został Sysprep z zainstalowanym Microsoft Monitoring Agent.

### <a name="resolution"></a>Rozdzielczość

1. Odwiedź stronę [Planowanie sieci](../automation-hybrid-runbook-worker.md#network-planning) , aby dowiedzieć się, które adresy i porty muszą być dozwolone, aby Update Management działały.
2. Jeśli używasz sklonowanego obrazu:
   1. W obszarze roboczym Log Analytics Usuń maszynę wirtualną z zapisanego wyszukiwania w obszarze Konfiguracja zakresu `MicrosoftDefaultScopeConfig-Updates`, jeśli jest wyświetlana. Zapisane wyszukiwania można znaleźć **ogólnie** w obszarze roboczym.
   2. Uruchom polecenie `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. Uruchom `Restart-Service HealthService`, aby ponownie uruchomić `HealthService`. Spowoduje to ponowne utworzenie klucza i wygenerowanie nowego identyfikatora UUID.
   4. Jeśli to nie zadziała, najpierw uruchom obraz, a następnie Zainstaluj agenta MMA po tym fakcie.

## <a name="multi-tenant"></a>Scenariusz: podczas tworzenia wdrożenia aktualizacji dla maszyn w innej dzierżawie platformy Azure występuje błąd połączonej subskrypcji.

### <a name="issue"></a>Problem

Podczas próby utworzenia wdrożenia aktualizacji dla maszyn w innej dzierżawie platformy Azure zostanie wyświetlony następujący błąd:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje w przypadku tworzenia wdrożenia aktualizacji z maszynami wirtualnymi platformy Azure w innej dzierżawie uwzględnionym w wdrożeniu aktualizacji.

### <a name="resolution"></a>Rozdzielczość

Aby zaplanowali te zadania, należy użyć następującego obejścia. Można użyć polecenia cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) z przełącznikiem `-ForUpdate`, aby utworzyć harmonogram, i użyć polecenia cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) i przekazać maszyny w innej dzierżawie do parametru `-NonAzureComputer`. Poniższy przykład pokazuje przykład, jak to zrobić:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>Scenariusz: nie wyjaśniono ponownych uruchomień

### <a name="issue"></a>Problem

Skonfigurowano **sterowanie ponownym uruchomieniem** bez **ponownego uruchamiania**, ale nadal trwa ponowne uruchamianie maszyn po zainstalowaniu aktualizacji.

### <a name="cause"></a>Przyczyna

Zachowanie Windows Update może być modyfikowane przez kilka kluczy rejestru, które mogą modyfikować zachowania ponownego uruchamiania.

### <a name="resolution"></a>Rozdzielczość

Zapoznaj się z kluczami rejestru wymienionymi w obszarze [Konfigurowanie aktualizacji automatycznych, edytując rejestr](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry) i [klucze rejestru używane do zarządzania ponownym uruchomieniem](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , aby upewnić się, że maszyny są prawidłowo skonfigurowane.

## <a name="failed-to-start"></a>Scenariusz: nie można uruchomić komputera w ramach wdrożenia aktualizacji

### <a name="issue"></a>Problem

**Uruchomienie komputera nie powiodło się** . Po wyświetleniu szczegółowych informacji o komputerze zostanie wyświetlony następujący błąd:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić z jednego z następujących powodów:

* Komputer nie istnieje już.
* Maszyna jest wyłączona i nieosiągalna.
* Komputer ma problem z łącznością sieciową, a hybrydowy proces roboczy na komputerze jest nieosiągalny.
* Wprowadzono aktualizację Microsoft Monitoring Agent, która zmieniła SourceComputerId
* Jeśli osiągnięto współbieżny limit zadań 2 000 w ramach konta usługi Automation, przebieg aktualizacji może zostać ograniczony. Każde wdrożenie jest uznawane za zadanie i każdy komputer w ramach wdrożenia aktualizacji jako zadanie. Wszystkie inne zadania usługi Automation lub wdrożenia, które są aktualnie uruchomione na koncie usługi Automation, w ramach współbieżnego limitu zadań.

### <a name="resolution"></a>Rozdzielczość

W przypadku zastosowania [grup dynamicznych](../automation-update-management-groups.md) do wdrożeń aktualizacji.

* Sprawdź, czy maszyna nadal istnieje i jest osiągalna. Jeśli nie istnieje, Edytuj wdrożenie i Usuń maszynę.
* Zapoznaj się z sekcją [Planowanie w sieci](../automation-update-management.md#ports) , aby uzyskać listę portów i adresów wymaganych do Update Management i sprawdź, czy maszyna spełnia te wymagania.
* Uruchom następujące zapytanie w Log Analytics, aby znaleźć maszyny w Twoim środowisku, których `SourceComputerId` uległy zmianie. Wyszukaj komputery mające taką samą wartość `Computer`, ale inną wartość `SourceComputerId`. Po znalezieniu odpowiednich maszyn należy edytować wdrożenia aktualizacji przeznaczone dla tych maszyn, a następnie usunąć i ponownie dodać maszyny, aby `SourceComputerId` odzwierciedla poprawną wartość.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

## <a name="updates-nodeployment"></a>Scenariusz: Instalacja aktualizacji bez wdrożenia

### <a name="issue"></a>Problem

Po zarejestrowaniu maszyny z systemem Windows w Update Management można zobaczyć aktualizacje instalacji bez wdrożenia.

### <a name="cause"></a>Przyczyna

W systemie Windows aktualizacje są instalowane automatycznie zaraz po ich udostępnieniu. Może to spowodować pomyłkę, jeśli aktualizacja nie została zaplanowana do wdrożenia na maszynie.

### <a name="resolution"></a>Rozdzielczość

Klucz rejestru systemu Windows, `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` domyślna wartość "4" — **Pobierz i zainstaluj**.

W przypadku klientów Update Management zalecamy ustawienie tego klucza na wartość "3" — **pobieranie z autopobieraniem, ale nie na automatycznej instalacji**.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie aktualizacji automatycznych](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="machine-already-registered"></a>Scenariusz: maszyna jest już zarejestrowana na innym koncie

### <a name="issue"></a>Problem

Zostanie wyświetlony następujący komunikat o błędzie:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Przyczyna

Maszyna została już dołączona do innego obszaru roboczego dla Update Management.

### <a name="resolution"></a>Rozdzielczość

1. Wykonaj kroki opisane w sekcji [maszyny nie są wyświetlane w portalu w obszarze Update Management](#nologs) , aby upewnić się, że komputer jest raportowany do prawidłowego obszaru roboczego.
2. Wykonaj czyszczenie starych artefaktów na maszynie, [usuwając hybrydową grupę elementów Runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) , a następnie spróbuj ponownie.

## <a name="machine-unable-to-communicate"></a>Scenariusz: komputer nie może nawiązać połączenia z usługą

### <a name="issue"></a>Problem

Zostanie wyświetlony jeden z następujących komunikatów o błędach:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

### <a name="cause"></a>Przyczyna

Może istnieć serwer proxy, Brama lub zapora blokująca komunikację sieciową.

### <a name="resolution"></a>Rozdzielczość

Przejrzyj sieć i upewnij się, że odpowiednie porty i adresy są dozwolone. Aby uzyskać listę portów i adresów wymaganych przez Update Management i hybrydowych procesów roboczych elementów Runbook, zobacz [wymagania dotyczące sieci](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="unable-to-create-selfsigned-cert"></a>Scenariusz: nie można utworzyć certyfikatu z podpisem własnym

### <a name="issue"></a>Problem

Zostanie wyświetlony jeden z następujących komunikatów o błędach:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Przyczyna

Hybrydowy proces roboczy elementu Runbook nie mógł wygenerować certyfikatu z podpisem własnym

### <a name="resolution"></a>Rozdzielczość

Sprawdź, czy konto systemowe ma dostęp do odczytu do folderu **C:\ProgramData\Microsoft\Crypto\RSA** , i spróbuj ponownie.

## <a name="mw-exceeded"></a>Scenariusz: zaplanowane zarządzanie aktualizacjami nie powiodło się z powodu błędu MaintenanceWindowExceeded

### <a name="issue"></a>Problem

Domyślne okno obsługi aktualizacji to 120 minut. Można zwiększyć okno obsługi do maksymalnie sześciu (6) godzin lub 360 minut.

### <a name="resolution"></a>Rozdzielczość

Edytuj wszystkie błędy zaplanowanej aktualizacji, a następnie zwiększ okno obsługi.

Aby uzyskać więcej informacji o oknach obsługi, zobacz [Install Updates](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="hresult"></a>Scenariusz: maszyna jest wyświetlana jako nieoceniona i pokazuje wyjątek HResult

### <a name="issue"></a>Problem

* Masz maszyny, które pokazują, że **nie oceniono** pod **względem zgodności**, i zobaczysz komunikat o wyjątku poniżej.
* Masz maszyny, które są wyświetlane jako nieoceniane
* W portalu zostanie wyświetlony kod błędu HRESULT.

### <a name="cause"></a>Przyczyna

Agent aktualizacji (Agent Windows Update w systemie Windows, Menedżer pakietów dla dystrybucji systemu Linux) nie został poprawnie skonfigurowany. Update Management polega na agencie aktualizacji maszyny w celu zapewnienia wymaganych aktualizacji, stanu poprawki oraz wyników wdrożonych poprawek. Bez tych informacji Update Management nie może poprawnie zgłosić poprawek wymaganych lub zainstalowanych.

### <a name="resolution"></a>Rozdzielczość

Podjęto próbę wykonania aktualizacji lokalnie na komputerze. Jeśli to się nie powiedzie, zazwyczaj oznacza to, że błąd konfiguracji agenta aktualizacji.

Typowe przyczyny niepowodzenia to:

* Konfiguracja sieci i zapory.
* W przypadku systemu Linux zapoznaj się z odpowiednią dokumentacją, aby upewnić się, że masz dostęp do punktu końcowego sieci repozytorium pakietu.
* W przypadku systemu Windows sprawdź konfigurację agenta wymienioną w temacie [aktualizacje nie są pobierane z punktu końcowego intranetu (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).
  * Jeśli maszyny są skonfigurowane do Windows Update, upewnij się, że masz dostęp do punktów końcowych wymienionych poniżej [ ](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Jeśli maszyny są skonfigurowane dla usług WSUS, upewnij się, że masz dostęp do serwera WSUS skonfigurowanego za pomocą [klucza rejestru WUServer](/windows/deployment/update/waas-wu-settings).

Jeśli zobaczysz wynik HRESULT, kliknij dwukrotnie wyjątek wyświetlany na czerwono, aby wyświetlić cały komunikat o wyjątku. Zapoznaj się z poniższą tabelą, aby uzyskać potencjalne rozwiązania lub akcje do wykonania:

|Wyjątek  |Rozwiązanie lub Akcja  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Przeszukaj odpowiedni kod błędu na [liście kodów błędów w usłudze Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) , aby znaleźć dodatkowe szczegóły dotyczące przyczyny wyjątku.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Te błędy są problemy z łącznością sieciową. Upewnij się, że komputer ma odpowiednią łączność sieciową do Update Management. Zapoznaj się z sekcją [Planowanie sieci](../automation-update-management.md#ports) , aby uzyskać listę wymaganych portów i adresów.        |
|`0x8024001E`| Operacja aktualizacji nie została ukończona, ponieważ trwa zamykanie usługi lub systemu.|
|`0x8024002E`| Usługa Windows Update jest wyłączona.|
|`0x8024402C`     | W przypadku korzystania z serwera programu WSUS upewnij się, że wartości rejestru dla `WUServer` i `WUStatusServer` w kluczu rejestru `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` mają prawidłowy serwer WSUS.        |
|`0x80072EE2`|Problem z połączeniem sieciowym lub problem z rozmową ze skonfigurowanym serwerem WSUS. Sprawdź ustawienia usług WSUS i upewnij się, że jest dostępny z poziomu klienta.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Upewnij się, że usługa Windows Update (wuauserv) jest uruchomiona i nie jest wyłączona.        |
|Każdy inny wyjątek ogólny     | Wykonaj wyszukiwanie w Internecie w poszukiwaniu możliwych rozwiązań i pracuj z lokalną pomocą techniczną IT.         |

Przeglądanie `windowsupdate.log` może pomóc w próbie ustalenia możliwej przyczyny. Aby uzyskać więcej informacji na temat odczytywania dziennika, zobacz [jak odczytać plik WindowsUpdate. log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Dodatkowo można pobrać i uruchomić narzędzie do [rozwiązywania problemów Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) , aby sprawdzić, czy występują problemy z Windows Update na komputerze.

> [!NOTE]
> [Windows Update narzędzie do rozwiązywania problemów](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) jest przeznaczone dla klientów z systemem Windows, ale działa również w systemie Windows Server.

## <a name="scenario-update-run-returns-status-failed"></a>Scenariusz: przebieg aktualizacji zwraca stan "Niepowodzenie"

### <a name="issue"></a>Problem

Rozpocznie się przebieg aktualizacji, ale napotka błędy podczas przebiegu.

### <a name="cause"></a>Przyczyna

Możliwe przyczyny:

* Menedżer pakietów jest w złej kondycji.
* Program Update Agent (WUA for Windows, dystrybucji — konkretny pakiet Manager dla systemu Linux) jest nieprawidłowo skonfigurowany.
* Określone pakiety mogą zakłócać stosowanie poprawek opartych na chmurze.
* Maszyna była nieosiągalna.
* Aktualizacje mają zależności, które nie zostały rozpoznane.

### <a name="resolution"></a>Rozdzielczość

Jeśli wystąpią błędy podczas przebiegu aktualizacji po pomyślnym uruchomieniu, [Sprawdź dane wyjściowe zadania](../manage-update-multi.md#view-results-of-an-update-deployment) z danego komputera w przebiegu. Możesz znaleźć określone komunikaty o błędach z maszyn, które można zbadać i podjąć odpowiednie działania. Update Management wymaga, aby Menedżer pakietów był w dobrej kondycji w przypadku pomyślnych wdrożeń aktualizacji.

Jeśli określone poprawki, pakiety lub aktualizacje są widoczne bezpośrednio przed zakończeniem zadania, można [wypróbować je z](../automation-tutorial-update-management.md#schedule-an-update-deployment) kolejnego wdrożenia aktualizacji. Aby zebrać informacje dziennika z Windows Update, zobacz [pliki dziennika Windows Update](/windows/deployment/update/windows-update-logs).

Jeśli nie możesz rozwiązać problemu z poprawkami, Utwórz kopię następującego pliku dziennika i Zachowaj ją **przed** rozpoczęciem następnego wdrożenia aktualizacji w celu rozwiązywania problemów:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-are-not-installed"></a>Nie zainstalowano poprawek

### <a name="machines-do-not-install-updates"></a>Na maszynach nie są instalowane aktualizacje

* Spróbuj uruchomić aktualizacje bezpośrednio na maszynie. Jeśli nie można zaktualizować maszyny, zapoznaj się z [listę potencjalnych błędów w przewodniku rozwiązywania problemów](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Jeśli aktualizacje są uruchamiane lokalnie, spróbuj usunąć i zainstalować ponownie agenta na maszynie, postępując zgodnie z instrukcjami w artykule [„Usuwanie maszyny wirtualnej z rozwiązania Update Management”](https://docs.microsoft.com/azure/automation/automation-update-management#remove-a-vm-from-update-management).

### <a name="i-know-updates-are-available-but-they-dont-show-as-needed-on-my-machines"></a>Wiemy, że aktualizacje są dostępne, ale nie są wyświetlane w razie konieczności na maszynach

* Często dzieje się tak, gdy maszyny zostały skonfigurowane do pobierania aktualizacji z usług WSUS/programu SCCM, ale aktualizacje nie zostały zatwierdzone przez usługi WSUS/program SCCM.
* Możesz sprawdzić, czy maszyny zostały skonfigurowane na potrzeby usług WSUS/programu SCCM, [korzystając z odsyłacza klucza rejestru „UseWUServer” do kluczy rejestru w sekcji „Configuring Automatic Updates by Editing the Registry” (Konfigurowanie aktualizacji automatycznych przez edytowanie rejestru) tego dokumentu](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)
* Jeśli aktualizacje nie są zatwierdzone w programie WSUS, nie zostaną zainstalowane. Niezatwierdzone aktualizacje można sprawdzić w Log Analytics z następującym zapytaniem.

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Aktualizacje są wyświetlane jako zainstalowane, ale nie mogę ich znaleźć na mojej maszynie

* Aktualizacje są często zastępowane przez inne aktualizacje. Aby uzyskać więcej informacji, zobacz [część „Update is superseded” (Aktualizacja została zastąpiona) w przewodniku Windows Update Troubleshooting (Rozwiązywanie problemów z witryną Windows Update)](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)

### <a name="installing-updates-by-classification-on-linux"></a>Instalowanie aktualizacji według klasyfikacji w systemie Linux

* Wdrażanie aktualizacji w systemie Linux według klasyfikacji („Aktualizacje krytyczne i zabezpieczeń”) ma ważne zastrzeżenia, szczególnie w przypadku systemu CentOS. Te [ograniczenia zostały opisane na stronie omówienia rozwiązania Update Management](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)

### <a name="kb2267602-is-consistently--missing"></a>KB2267602 jest ciągle nieobecny

* KB2267602 to [aktualizacja definicji usługi Windows Defender](https://www.microsoft.com/wdsi/definitions). Jest ona aktualizowana codziennie.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
