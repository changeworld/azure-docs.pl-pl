---
title: Rozwiązywanie problemów z Update Management
description: Dowiedz się, jak rozwiązywać problemy z Update Management.
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 2aebcf05cbc818997943ed3bab19fb1fd8a83592
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786061"
---
# <a name="troubleshooting-issues-with-update-management"></a>Rozwiązywanie problemów z Update Management

W tym artykule omówiono rozwiązania problemów, które mogą wystąpić podczas korzystania z Update Management.

Istnieje narzędzie do rozwiązywania problemów z agentem dla agenta hybrydowego procesu roboczego w celu ustalenia podstawowego problemu. Aby dowiedzieć się więcej na temat narzędzia do rozwiązywania problemów, zobacz [Rozwiązywanie problemów z aktualizowaniem agentów](update-agent-issues.md). W przypadku wszystkich innych problemów należy skorzystać z poniższych wskazówek dotyczących rozwiązywania problemów.

Jeśli wystąpią problemy podczas próby dołączenia rozwiązania na maszynie wirtualnej (VM), sprawdź dziennik **Operations Manager** w obszarze **Dzienniki aplikacji i usług** na komputerze lokalnym, aby poznać zdarzenia o identyfikatorze 4502 i szczegółowe informacje o zdarzeniach, które zawierają **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent**.

W poniższej sekcji przedstawiono określone komunikaty o błędach i możliwe rozwiązania dla każdej z nich. Inne problemy z dołączaniem można znaleźć w temacie [Rozwiązywanie problemów z](onboarding.md)dołączaniem rozwiązania.

## <a name="nologs"></a>Scenariusz: maszyny nie są wyświetlane w portalu w obszarze Update Management

### <a name="issue"></a>Problem

Występują następujące objawy:

* Komputer nie jest **skonfigurowany** na podstawie Update Management widoku maszyny wirtualnej.

* Brak Twoich maszyn w widoku Update Management konta Azure Automation.

* Masz maszyny, które pokazują, że **nie są oceniane** pod kątem **zgodności**. Dane pulsu można jednak znaleźć w dziennikach Azure Monitor dla hybrydowego procesu roboczego elementu Runbook, ale nie dla Update Management.

### <a name="cause"></a>Przyczyna

Przyczyną tego problemu mogą być lokalne problemy z konfiguracją lub nieprawidłowo skonfigurowana konfiguracja zakresu.

Może być konieczne ponowne zarejestrowanie i ponowna instalacja hybrydowego procesu roboczego elementu Runbook.

Być może zdefiniowano limit przydziału w obszarze roboczym, który został osiągnięty i uniemożliwia dalsze przechowywanie danych.

### <a name="resolution"></a>Rozdzielczość

* Uruchom narzędzie do rozwiązywania problemów dla [systemu Windows](update-agent-issues.md#troubleshoot-offline) lub [Linux](update-agent-issues-linux.md#troubleshoot-offline), w zależności od systemu operacyjnego.

* Upewnij się, że komputer jest raportowany do prawidłowego obszaru roboczego. Aby uzyskać wskazówki dotyczące weryfikacji tego aspektu, zobacz [Weryfikowanie łączności agenta z log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Upewnij się również, że ten obszar roboczy jest połączony z kontem Azure Automation. Aby potwierdzić, przejdź do konta usługi Automation i wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby**.

* Upewnij się, że maszyny są widoczne w obszarze roboczym Log Analytics. Uruchom następujące zapytanie w obszarze roboczym Log Analytics, który jest połączony z kontem usługi Automation:

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
  Jeśli komputer nie jest widoczny w wynikach zapytania, nie został ostatnio zaewidencjonowany, co oznacza, że prawdopodobnie wystąpił problem z konfiguracją lokalną i należy [ponownie zainstalować agenta](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Jeśli komputer jest wyświetlany w wynikach zapytania, należy zweryfikować konfigurację zakresu określoną w następnym punktowanym elemencie na tej liście.

* Sprawdź, czy występują problemy z konfiguracją zakresu. [Konfiguracja zakresu](../automation-onboard-solutions-from-automation-account.md#scope-configuration) określa, które maszyny mają zostać skonfigurowane dla rozwiązania. Jeśli Twoja maszyna jest wyświetlana w obszarze roboczym, ale nie w portalu **Update Management** , musisz skonfigurować konfigurację zakresu w celu kierowania maszyn. Aby dowiedzieć się, jak to zrobić, zobacz sekcję dołączanie [maszyn w obszarze roboczym](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* W obszarze roboczym Uruchom następujące zapytanie:

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```
  Jeśli uzyskasz `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`, osiągnięto limit przydziału zdefiniowany w Twoim obszarze roboczym i że zarejestrowano dane. W obszarze roboczym przejdź do pozycji **użycie i szacowane koszty**  > **Zarządzanie ilością danych** i sprawdź swój limit przydziału lub usuń go.

* Jeśli te kroki nie rozwiążą problemu, wykonaj kroki opisane w sekcji [wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows](../automation-windows-hrw-install.md) w celu ponownego zainstalowania hybrydowego procesu roboczego dla systemu Windows. Lub w przypadku systemu Linux [Wdróż hybrydowy proces roboczy elementu Runbook systemu Linux](../automation-linux-hrw-install.md).

## <a name="rp-register"></a>Scenariusz: nie można zarejestrować dostawcy zasobów usługi Automation dla subskrypcji

### <a name="issue"></a>Problem

Gdy pracujesz z rozwiązaniami na koncie usługi Automation, wystąpi następujący błąd:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Przyczyna

Dostawca zasobów usługi Automation nie jest zarejestrowany w subskrypcji.

### <a name="resolution"></a>Rozdzielczość

Aby zarejestrować dostawcę zasobów usługi Automation, wykonaj następujące kroki w Azure Portal:

1. Na liście usługi platformy Azure w dolnej części portalu wybierz pozycję **wszystkie usługi**, a następnie wybierz pozycję **subskrypcje** w grupie usługi ogólne.
2. Wybierz subskrypcję.
3. W obszarze **Ustawienia**wybierz pozycję **dostawcy zasobów**.
4. Z listy dostawców zasobów Sprawdź, czy dostawca zasobów **Microsoft. Automation** jest zarejestrowany.
5. Jeśli nie ma go na liście, zarejestruj dostawcę **Microsoft. Automation** , wykonując czynności opisane w sekcji [Rozwiązywanie problemów dotyczących rejestracji dostawcy zasobów](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="components-enabled-not-working"></a>Scenariusz: składniki dla Update Management rozwiązania zostały włączone i teraz ta maszyna wirtualna jest konfigurowana

### <a name="issue"></a>Problem

Po 15 minutach po dołączeniu do maszyny wirtualnej nadal zobaczysz następujący komunikat:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić z następujących powodów:

- Trwa blokowanie komunikacji z kontem usługi Automation.
- Dołączona maszyna wirtualna może pochodzić ze sklonowanego komputera, który nie został Sysprep z zainstalowanym Microsoft Monitoring Agent (MMA).

### <a name="resolution"></a>Rozdzielczość

1. Przejdź do pozycji [Planowanie sieci](../automation-hybrid-runbook-worker.md#network-planning) , aby dowiedzieć się, które adresy i porty muszą być dozwolone, aby Update Management działały.
2. Jeśli używasz sklonowanego obrazu:
   1. W obszarze roboczym Log Analytics Usuń maszynę wirtualną z zapisanego wyszukiwania konfiguracja zakresu `MicrosoftDefaultScopeConfig-Updates`, jeśli zostanie ona wyświetlona. Zapisane wyszukiwania można znaleźć **ogólnie** w obszarze roboczym.
   2. Uruchom polecenie `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`.
   3. Uruchom `Restart-Service HealthService`, aby ponownie uruchomić `HealthService`. Spowoduje to odtworzenie klucza i wygenerowanie nowego identyfikatora UUID.
   4. Jeśli takie podejście nie działa, najpierw należy uruchomić program Sysprep na obrazie, a następnie zainstalować MMA.

## <a name="multi-tenant"></a>Scenariusz: Wystąpił błąd połączonej subskrypcji podczas tworzenia wdrożenia aktualizacji dla maszyn w innej dzierżawie platformy Azure

### <a name="issue"></a>Problem

Wystąpił następujący błąd podczas próby utworzenia wdrożenia aktualizacji dla maszyn w innej dzierżawie platformy Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas tworzenia wdrożenia aktualizacji z maszynami wirtualnymi platformy Azure w innej dzierżawie uwzględnionej w wdrożeniu aktualizacji.

### <a name="resolution"></a>Rozdzielczość

Aby zaplanowano te elementy, należy wykonać poniższe obejście. Aby utworzyć harmonogram, można użyć polecenia cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) z przełącznikiem `-ForUpdate`. Następnie użyj polecenia cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) i przekaż maszyny w innej dzierżawie do parametru `-NonAzureComputer`. Poniższy przykład pokazuje, jak to zrobić:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>Scenariusz: nie wyjaśniono ponownych uruchomień

### <a name="issue"></a>Problem

Mimo że ustawisz opcję **kontroli ponownego uruchamiania** na **nigdy nie uruchamiaj ponownie**, maszyny nadal są ponownie uruchamiane po zainstalowaniu aktualizacji.

### <a name="cause"></a>Przyczyna

Windows Update mogą być modyfikowane przez kilka kluczy rejestru, z których każde może zmodyfikować zachowanie ponownego uruchamiania.

### <a name="resolution"></a>Rozdzielczość

Zapoznaj się z kluczami rejestru wymienionymi w obszarze [Konfigurowanie aktualizacji automatycznych, edytując rejestr](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) i [klucze rejestru używane do zarządzania ponownym uruchomieniem](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , aby upewnić się, że maszyny są prawidłowo skonfigurowane.

## <a name="failed-to-start"></a>Scenariusz: na komputerze nie można uruchomić programu w ramach wdrożenia aktualizacji

### <a name="issue"></a>Problem

Na maszynie zostanie wyświetlony stan **nie można uruchomić** . Po wyświetleniu szczegółowych informacji o komputerze zostanie wyświetlony następujący błąd:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Przyczyna

Ten błąd może mieć jedną z następujących przyczyn:

* Komputer nie istnieje już.
* Maszyna jest wyłączona i nieosiągalna.
* Komputer ma problem z łącznością sieciową i w związku z tym hybrydowy proces roboczy na komputerze jest nieosiągalny.
* Wprowadzono aktualizację MMA, która zmieniła SourceComputerId.
* Działanie przebiegu aktualizacji zostało ograniczone, jeśli osiągnięto limit 2 000 zadań współbieżnych na koncie usługi Automation. Każde wdrożenie jest uznawane za zadanie, a każdy komputer w ramach wdrożenia aktualizacji liczy się jako zadanie. Wszystkie inne zadania usługi Automation lub wdrożenia, które są aktualnie uruchomione na koncie usługi Automation, są wliczane do współbieżnego limitu zadań.

### <a name="resolution"></a>Rozdzielczość

W razie potrzeby użyj [grup dynamicznych](../automation-update-management-groups.md) do wdrożeń aktualizacji. Ponadto

* Sprawdź, czy maszyna nadal istnieje i jest osiągalna. Jeśli nie istnieje, Edytuj wdrożenie i Usuń maszynę.
* Zapoznaj się z sekcją [Planowanie sieci](../automation-update-management.md#ports) , aby uzyskać listę portów i adresów wymaganych do Update Management, a następnie sprawdź, czy maszyna spełnia te wymagania.
* Uruchom następujące zapytanie w Log Analytics, aby znaleźć maszyny w środowisku, których `SourceComputerId` uległy zmianie. Wyszukaj komputery mające taką samą `Computer` wartość, ale inną wartość `SourceComputerId`. 

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```
   Po znalezieniu odpowiednich maszyn należy zmodyfikować wdrożenia aktualizacji przeznaczone dla tych maszyn, a następnie je usunąć i dodać ponownie, tak aby `SourceComputerId` odzwierciedlały poprawną wartość.

## <a name="updates-nodeployment"></a>Scenariusz: aktualizacje są instalowane bez wdrożenia

### <a name="issue"></a>Problem

Po zarejestrowaniu maszyny z systemem Windows w Update Management są widoczne aktualizacje zainstalowane bez wdrożenia.

### <a name="cause"></a>Przyczyna

W systemie Windows aktualizacje są instalowane automatycznie zaraz po ich udostępnieniu. Takie zachowanie może spowodować pomyłkę, jeśli aktualizacja nie została zaplanowana do wdrożenia na komputerze.

### <a name="resolution"></a>Rozdzielczość

Klucz rejestru `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` jest domyślnie ustawiony na wartość 4: **Pobierz i zainstaluj**.

W przypadku klientów Update Management zalecamy ustawienie tego klucza na 3: **pobieranie z autopobieraniem, ale nie na automatycznej instalacji**.

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
2. Wyczyść stare artefakty na maszynie, [usuwając grupę hybrydowych elementów Runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group), a następnie spróbuj ponownie.

## <a name="machine-unable-to-communicate"></a>Scenariusz: maszyna nie może komunikować się z usługą

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

Serwer proxy, Brama lub Zapora mogą blokować komunikację sieciową.

### <a name="resolution"></a>Rozdzielczość

Przejrzyj sieć i upewnij się, że odpowiednie porty i adresy są dozwolone. Aby uzyskać listę portów i adresów wymaganych przez Update Management i hybrydowych procesów roboczych elementów Runbook, zobacz [wymagania sieciowe](../automation-hybrid-runbook-worker.md#network-planning) .

## <a name="unable-to-create-selfsigned-cert"></a>Scenariusz: nie można utworzyć certyfikatu z podpisem własnym

### <a name="issue"></a>Problem

Zostanie wyświetlony jeden z następujących komunikatów o błędach:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Przyczyna

Hybrydowy proces roboczy elementu Runbook nie może wygenerować certyfikatu z podpisem własnym.

### <a name="resolution"></a>Rozdzielczość

Sprawdź, czy konto systemowe ma dostęp do odczytu do folderu **C:\ProgramData\Microsoft\Crypto\RSA** , i spróbuj ponownie.

## <a name="mw-exceeded"></a>Scenariusz: zaplanowana aktualizacja nie powiodła się z powodu błędu MaintenanceWindowExceeded

### <a name="issue"></a>Problem

Domyślne okno obsługi aktualizacji to 120 minut. Można zwiększyć okno obsługi do maksymalnie 6 godzin lub 360 minut.

### <a name="resolution"></a>Rozdzielczość

Edytuj wszystkie błędy zaplanowanej aktualizacji, a następnie zwiększ okno obsługi.

Aby uzyskać więcej informacji o oknach obsługi, zobacz [Install Updates](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="hresult"></a>Scenariusz: maszyna jest wyświetlana jako "nieoceniona" i pokazuje wyjątek HResult

### <a name="issue"></a>Problem

* Masz maszyny, które pokazują, że **nie oceniono** pod **względem zgodności**, i zobaczysz komunikat o wyjątku poniżej.
* Masz maszyny, które pokazują, że nie oceniono.
* W portalu zostanie wyświetlony kod błędu HRESULT.

### <a name="cause"></a>Przyczyna

Agent aktualizacji (Agent Windows Update w systemie Windows; Menedżer pakietów dla dystrybucji systemu Linux) nie jest prawidłowo skonfigurowany. Update Management polega na agencie aktualizacji maszyny w celu zapewnienia wymaganych aktualizacji, stanu poprawki oraz wyników wdrożonych poprawek. Bez tych informacji Update Management nie może poprawnie zgłosić poprawek wymaganych lub zainstalowanych.

### <a name="resolution"></a>Rozdzielczość

Spróbuj przeprowadzić aktualizacje lokalnie na komputerze. Jeśli to się nie powiedzie, zazwyczaj oznacza to, że wystąpił błąd konfiguracji agenta aktualizacji.

Ten problem jest często spowodowany przez problemy z konfiguracją sieci i zaporą. Spróbuj wykonać następujące kroki:

* W przypadku systemu Linux zapoznaj się z odpowiednią dokumentacją, aby upewnić się, że można uzyskać dostęp do punktu końcowego sieci repozytorium pakietu.
* W przypadku systemu Windows sprawdź konfigurację agenta wymienioną w temacie [aktualizacje nie są pobierane z punktu końcowego intranetu (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).
  * Jeśli maszyny są skonfigurowane do Windows Update, upewnij się, że można uzyskać dostęp do punktów końcowych opisanych w [problemach związanych z protokołem HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Jeśli na komputerach skonfigurowano program Windows Server Update Services (WSUS), upewnij się, że można nawiązać połączenie z serwerem WSUS skonfigurowanym za pomocą [klucza rejestru WUServer](/windows/deployment/update/waas-wu-settings).

Jeśli zobaczysz wynik HRESULT, kliknij dwukrotnie wyjątek wyświetlany na czerwono, aby wyświetlić cały komunikat o wyjątku. Zapoznaj się z poniższą tabelą dotyczącą potencjalnych rozwiązań lub zalecanych działań:

|Wyjątek  |Rozwiązanie lub Akcja  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Przeszukaj odpowiedni kod błędu na [liście kodów błędów w usłudze Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) , aby znaleźć dodatkowe szczegóły dotyczące przyczyny wyjątku.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Wskazują one problemy z łącznością sieciową. Upewnij się, że maszyna ma łączność sieciową z Update Management. Lista wymaganych portów i adresów znajduje się w sekcji [Planowanie sieci](../automation-update-management.md#ports) .        |
|`0x8024001E`| Operacja aktualizacji nie została ukończona, ponieważ trwa zamykanie usługi lub systemu.|
|`0x8024002E`| Usługa Windows Update jest wyłączona.|
|`0x8024402C`     | W przypadku korzystania z serwera programu WSUS upewnij się, że wartości rejestru `WUServer` i `WUStatusServer` w kluczu rejestru `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` określają poprawny serwer WSUS.        |
|`0x80072EE2`|Występuje problem z łącznością sieciową lub problem podczas rozmowy ze skonfigurowanym serwerem WSUS. Sprawdź ustawienia usług WSUS i upewnij się, że usługa jest dostępna z poziomu klienta.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Upewnij się, że usługa Windows Update (wuauserv) jest uruchomiona i nie jest wyłączona.        |
|Każdy inny wyjątek ogólny     | Uruchom wyszukiwanie w Internecie, aby zapoznać się z możliwymi rozwiązaniami, i pracuj z lokalną pomocą techniczną IT.         |

Przeglądanie pliku Windowsupdate. log może również pomóc w ustaleniu możliwych przyczyn. Aby uzyskać więcej informacji na temat odczytywania dziennika, zobacz [jak odczytać plik WindowsUpdate. log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Możesz również pobrać i uruchomić narzędzie do [rozwiązywania problemów Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) , aby sprawdzić, czy występują problemy z Windows Update na komputerze.

> [!NOTE]
> Dokumentacja [Windows Update narzędzia do rozwiązywania problemów](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) wskazuje, że jest ona używana na klientach z systemem Windows, ale działa również w systemie Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scenariusz: przebieg aktualizacji zwraca stan "Niepowodzenie" (system Linux)

### <a name="issue"></a>Problem

Przebieg aktualizacji rozpocznie się, ale napotka błędy podczas przebiegu.

### <a name="cause"></a>Przyczyna

Możliwe przyczyny:

* Menedżer pakietów jest w złej kondycji.
* Program Update Agent (WUA for Windows, dystrybucji — konkretny pakiet Manager dla systemu Linux) jest nieprawidłowo skonfigurowany.
* Określone pakiety zakłócają stosowanie poprawek opartych na chmurze.
* Komputer jest nieosiągalny.
* Aktualizacje mają zależności, które nie zostały rozpoznane.

### <a name="resolution"></a>Rozdzielczość

Jeśli wystąpią błędy podczas przebiegu aktualizacji po pomyślnym uruchomieniu, [Sprawdź dane wyjściowe zadania](../manage-update-multi.md#view-results-of-an-update-deployment) z danego komputera w przebiegu. Możesz znaleźć określone komunikaty o błędach z maszyn, które można zbadać i podjąć odpowiednie działania. Update Management wymaga, aby Menedżer pakietów był w dobrej kondycji w przypadku pomyślnych wdrożeń aktualizacji.

Jeśli określone poprawki, pakiety lub aktualizacje są widoczne bezpośrednio przed zakończeniem zadania, można [wypróbować je z](../automation-tutorial-update-management.md#schedule-an-update-deployment) kolejnego wdrożenia aktualizacji. Aby zebrać informacje dziennika z Windows Update, zobacz [pliki dziennika Windows Update](/windows/deployment/update/windows-update-logs).

Jeśli nie możesz rozwiązać problemu z poprawkami, Utwórz kopię następującego pliku dziennika i Zachowaj ją w celu rozwiązywania problemów *przed* rozpoczęciem następnego wdrożenia aktualizacji:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>Nie zainstalowano poprawek

### <a name="machines-dont-install-updates"></a>Maszyny nie instalują aktualizacji

* Spróbuj uruchomić aktualizacje bezpośrednio na maszynie. Jeśli na komputerze nie można zastosować aktualizacji, zapoznaj się z [listą potencjalnych błędów w przewodniku rozwiązywania problemów](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Jeśli aktualizacje są uruchamiane lokalnie, spróbuj usunąć i ponownie zainstalować agenta na komputerze, postępując zgodnie ze wskazówkami w temacie [usuwanie maszyny wirtualnej z Update Management](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Wiemy, że aktualizacje są dostępne, ale nie są wyświetlane jako dostępne na moich maszynach

* Często zdarza się to, gdy maszyny są skonfigurowane do pobierania aktualizacji z usług WSUS lub System Center Configuration Manager (SCCM), ale programy WSUS i SCCM nie zaakceptowały aktualizacji.
* Możesz sprawdzić, czy maszyny są skonfigurowane pod kątem usług WSUS i SCCM, przez [krzyżowe odwołując klucz rejestru UseWUServer do kluczy rejestru w sekcji "Konfigurowanie aktualizacji automatycznych przez edytowanie rejestru" w tym artykule](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).
* Jeśli aktualizacje nie są zatwierdzone w programie WSUS, nie będą instalowane. Niezatwierdzone aktualizacje można sprawdzić w Log Analytics, uruchamiając następujące zapytanie:

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Aktualizacje są wyświetlane jako zainstalowane, ale nie mogę ich znaleźć na mojej maszynie

* Aktualizacje są często zastępowane przez inne aktualizacje. Aby uzyskać więcej informacji, zobacz [Aktualizacja została zastąpiona](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) w przewodniku rozwiązywania problemów Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Instalowanie aktualizacji według klasyfikacji w systemie Linux

* Wdrażanie aktualizacji w systemie Linux według klasyfikacji („Aktualizacje krytyczne i zabezpieczeń”) ma ważne zastrzeżenia, szczególnie w przypadku systemu CentOS. Te ograniczenia są udokumentowane na [stronie przegląd Update Management](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 jest ciągle nieobecny

* KB2267602 to [aktualizacja definicji usługi Windows Defender](https://www.microsoft.com/wdsi/definitions). Jest ona aktualizowana codziennie.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz się z [@AzureSupport](https://twitter.com/azuresupport), oficjalnego konta Microsoft Azure w celu poprawy obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
