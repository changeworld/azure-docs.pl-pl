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
ms.openlocfilehash: c6a76f4188ecbf6ca778fdbcd23ac9fed2f60dde
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534663"
---
# <a name="troubleshooting-issues-with-update-management"></a>Rozwiązywanie problemów z Update Management

W tym artykule omówiono rozwiązania rozwiązywania problemów, które mogą być wykonywane w przypadku korzystania z Update Management.

Istnieje narzędzie do rozwiązywania problemów z agentem dla agenta hybrydowego procesu roboczego w celu ustalenia podstawowego problemu. Aby dowiedzieć się więcej na temat narzędzia do rozwiązywania problemów, zobacz [Rozwiązywanie problemów z aktualizowaniem agentów](update-agent-issues.md). Wszystkie inne problemy znajdują się poniżej w szczegółowych informacjach o możliwych problemach.

## <a name="general"></a>Ogólne

### <a name="rp-register"></a>Scenariusz Nie można zarejestrować dostawcy zasobów usługi Automation dla subskrypcji

#### <a name="issue"></a>Problem

Podczas pracy z rozwiązaniami na koncie usługi Automation może zostać wyświetlony następujący błąd.

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

#### <a name="cause"></a>Przyczyna

Dostawca zasobów usługi Automation nie jest zarejestrowany w subskrypcji.

#### <a name="resolution"></a>Rozwiązanie

Dostawców zasobów usługi Automation można zarejestrować, wykonując następujące kroki w Azure Portal:

1. Kliknij pozycję **wszystkie usługi** na dolnej liście usług platformy Azure, a następnie wybierz pozycję **subskrypcje** w grupie usługi _Ogólne_ .
2. Wybierz subskrypcję.
3. Kliknij pozycję **dostawcy zasobów** w obszarze _Ustawienia_.
4. Z listy dostawców zasobów Sprawdź, czy jest zarejestrowany dostawca zasobów **Microsoft. Automation** .
5. Jeśli dostawca nie znajduje się na liście, zarejestruj dostawcę **Microsoft. Automation** , wykonując czynności opisane w [ ](/azure/azure-resource-manager/resource-manager-register-provider-errors)sekcji.

### <a name="mw-exceeded"></a>Scenariusz Zaplanowane zarządzanie aktualizacjami nie powiodło się z powodu błędu MaintenanceWindowExceeded

#### <a name="issue"></a>Problem

Domyślne okno obsługi aktualizacji to 120 minut. Można zwiększyć okno obsługi do maksymalnie sześciu (6) godzin lub 360 minut.

#### <a name="resolution"></a>Rozwiązanie

Edytuj wszystkie błędy zaplanowanej aktualizacji, a następnie zwiększ okno obsługi.

Aby uzyskać więcej informacji o oknach obsługi, zobacz [Install Updates](../automation-update-management.md#install-updates).

### <a name="components-enabled-not-working"></a>Scenariusz Składniki rozwiązania "Update Management" zostały włączone i teraz ta maszyna wirtualna jest konfigurowana

#### <a name="issue"></a>Problem

Po 15 minutach po dołączeniu do maszyny wirtualnej nadal zobaczysz następujący komunikat:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Przyczyna

Przyczyną tego błędu mogą być następujące przyczyny:

1. Trwa blokowanie komunikacji z powrotem do konta usługi Automation.
2. Dołączana maszyna wirtualna może pochodzić ze sklonowanego komputera, który nie został Sysprep z zainstalowanym Microsoft Monitoring Agent.

#### <a name="resolution"></a>Rozwiązanie

1. Odwiedź stronę [Planowanie sieci](../automation-hybrid-runbook-worker.md#network-planning) , aby dowiedzieć się, które adresy i porty muszą być dozwolone, aby Update Management działały.
2. Jeśli używasz sklonowanego obrazu:
   1. W obszarze roboczym log Analytics Usuń maszynę wirtualną z zapisanego wyszukiwania konfiguracja `MicrosoftDefaultScopeConfig-Updates` zakresu, jeśli jest wyświetlana. Zapisane wyszukiwania można znaleźć **ogólnie** w obszarze roboczym.
   2. Uruchom `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. Uruchom `Restart-Service HealthService` ,`HealthService`aby ponownie uruchomić. Spowoduje to ponowne utworzenie klucza i wygenerowanie nowego identyfikatora UUID.
   4. Jeśli to nie zadziała, najpierw uruchom obraz, a następnie Zainstaluj agenta MMA po tym fakcie.

### <a name="multi-tenant"></a>Scenariusz Wystąpił błąd połączonej subskrypcji podczas tworzenia wdrożenia aktualizacji dla maszyn w innej dzierżawie platformy Azure.

#### <a name="issue"></a>Problem

Podczas próby utworzenia wdrożenia aktualizacji dla maszyn w innej dzierżawie platformy Azure zostanie wyświetlony następujący błąd:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje w przypadku tworzenia wdrożenia aktualizacji z maszynami wirtualnymi platformy Azure w innej dzierżawie uwzględnionym w wdrożeniu aktualizacji.

#### <a name="resolution"></a>Rozwiązanie

Aby zaplanowali te zadania, należy użyć następującego obejścia. Możesz użyć polecenia cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) z przełącznikiem `-ForUpdate` [, aby utworzyć harmonogram, i użyć polecenia cmdlet New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) i przekazać maszyny w innym dzierżawca do `-NonAzureComputer` parametru. Poniższy przykład pokazuje przykład, jak to zrobić:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>Scenariusz Maszyny nie są wyświetlane w portalu w obszarze Update Management

#### <a name="issue"></a>Problem

Może działać w następujących scenariuszach:

* Komputer nie jest **skonfigurowany** na podstawie Update Management widoku maszyny wirtualnej

* Brak Twoich maszyn w widoku Update Management konta usługi Automation

* Masz maszyny, które pokazują, że **nie oceniono** pod **względem zgodności**, ale widzisz dane pulsu w dziennikach Azure monitor dla hybrydowego procesu roboczego elementu Runbook, ale nie Update Management.

#### <a name="cause"></a>Przyczyna

Może to być spowodowane potencjalnymi problemami z konfiguracją lokalną lub nieprawidłowo skonfigurowanym konfiguracją zakresu.

Może zajść konieczność ponownego zarejestrowania hybrydowego procesu roboczego elementu Runbook i ponownej instalacji.

W obszarze roboczym możesz zdefiniować przydział, który został osiągnięty i zatrzymał przechowywanie danych.

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że komputer jest raportowany do prawidłowego obszaru roboczego. Sprawdź, w jakim obszarze roboczym jest raportowany komputer. Aby uzyskać instrukcje dotyczące sposobu weryfikacji, zobacz [Weryfikowanie łączności agenta z log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Następnie upewnij się, że jest to obszar roboczy, który jest połączony z Twoim kontem Azure Automation. Aby to potwierdzić, przejdź do konta usługi Automation, a następnie kliknij pozycję **połączony obszar roboczy** w obszarze **powiązane zasoby**.

* Zaznacz, aby upewnić się, że maszyny są widoczne w obszarze roboczym Log Analytics. Uruchom następujące zapytanie w obszarze roboczym Log Analytics, który jest połączony z kontem usługi Automation. Jeśli komputer nie jest wyświetlany w wynikach zapytania, komputer nie wykonuje pulsu, co oznacza, że występuje prawdopodobnie problem z konfiguracją lokalną. Narzędzie do rozwiązywania problemów można uruchomić w [systemie Windows](update-agent-issues.md#troubleshoot-offline) lub [Linux](update-agent-issues-linux.md#troubleshoot-offline) , w zależności od systemu operacyjnego, lub można [zainstalować agenta ponownie](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Jeśli komputer jest wyświetlany w wynikach zapytania, należy określić konfigurację zakresu określoną w następującym punkcie.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* Sprawdź, czy występują problemy z konfiguracją zakresu. [Konfiguracja zakresu](../automation-onboard-solutions-from-automation-account.md#scope-configuration) określa, które maszyny mają zostać skonfigurowane dla rozwiązania. Jeśli Twoja maszyna jest wyświetlana w obszarze roboczym, ale nie jest wyświetlana, należy skonfigurować konfigurację zakresu w celu kierowania maszyn. Aby dowiedzieć się, jak to zrobić, zobacz sekcję dołączanie [maszyn w obszarze roboczym](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Jeśli powyższe kroki nie rozwiążą problemu, wykonaj kroki opisane w sekcji [wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows](../automation-windows-hrw-install.md) w celu ponownego zainstalowania hybrydowego procesu roboczego dla systemu Windows lub [wdrożenia hybrydowego procesu roboczego elementu Runbook](../automation-linux-hrw-install.md) w systemie Linux.

* W obszarze roboczym Uruchom następujące zapytanie. Jeśli zobaczysz wynik `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` , że masz limit przydziału zdefiniowany w Twoim obszarze roboczym, który został osiągnięty i zatrzymano zapisywanie danych. W obszarze roboczym przejdź do opcji **użycie i szacowane koszty** > **Zarządzanie ilością danych** , a następnie sprawdź limit przydziału lub Usuń posiadany limit przydziału.

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

## <a name="windows"></a>Windows

Jeśli wystąpią problemy podczas próby dołączenia rozwiązania na maszynie wirtualnej, sprawdź dziennik zdarzeń **Operations Manager** w obszarze **Dzienniki aplikacji i usług** na komputerze lokalnym, aby uzyskać zdarzenia z identyfikatorem zdarzenia **4502** i komunikatem o zdarzeniu zawierający **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent**.

W poniższej sekcji przedstawiono określone komunikaty o błędach i możliwe rozwiązanie dla każdego z nich. Aby poznać inne problemy z dołączaniem, zobacz temat [Rozwiązywanie problemów z](onboarding.md)dołączaniem rozwiązania.

### <a name="machine-already-registered"></a>Scenariusz Maszyna jest już zarejestrowana na innym koncie

#### <a name="issue"></a>Problem

Zostanie wyświetlony następujący komunikat o błędzie:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Przyczyna

Maszyna została już dołączona do innego obszaru roboczego dla Update Management.

#### <a name="resolution"></a>Rozwiązanie

Wykonaj czyszczenie starych artefaktów na maszynie, [usuwając hybrydową grupę elementów Runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) , a następnie spróbuj ponownie.

### <a name="machine-unable-to-communicate"></a>Scenariusz Komputer nie może nawiązać połączenia z usługą

#### <a name="issue"></a>Problem

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

#### <a name="cause"></a>Przyczyna

Może istnieć serwer proxy, Brama lub zapora blokująca komunikację sieciową.

#### <a name="resolution"></a>Rozwiązanie

Przejrzyj sieć i upewnij się, że odpowiednie porty i adresy są dozwolone. Aby uzyskać listę portów i adresów wymaganych przez Update Management i hybrydowych procesów roboczych elementów Runbook, zobacz [wymagania dotyczące sieci](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="unable-to-create-selfsigned-cert"></a>Scenariusz Nie można utworzyć certyfikatu z podpisem własnym

#### <a name="issue"></a>Problem

Zostanie wyświetlony jeden z następujących komunikatów o błędach:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Przyczyna

Hybrydowy proces roboczy elementu Runbook nie mógł wygenerować certyfikatu z podpisem własnym

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy konto systemowe ma dostęp do odczytu do folderu **C:\ProgramData\Microsoft\Crypto\RSA** , i spróbuj ponownie.

### <a name="failed-to-start"></a>Scenariusz Nie można uruchomić komputera w ramach wdrożenia aktualizacji

#### <a name="issue"></a>Problem

**Uruchomienie komputera nie powiodło się** . Po wyświetleniu szczegółowych informacji o komputerze zostanie wyświetlony następujący błąd:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić z jednego z następujących powodów:

* Komputer nie istnieje już.
* Maszyna jest wyłączona i nieosiągalna.
* Komputer ma problem z łącznością sieciową, a hybrydowy proces roboczy na komputerze jest nieosiągalny.
* Wprowadzono aktualizację Microsoft Monitoring Agent, która zmieniła SourceComputerId
* Jeśli osiągnięto współbieżny limit zadań 2 000 w ramach konta usługi Automation, przebieg aktualizacji może zostać ograniczony. Każde wdrożenie jest uznawane za zadanie i każdy komputer w ramach wdrożenia aktualizacji jako zadanie. Wszystkie inne zadania usługi Automation lub wdrożenia, które są aktualnie uruchomione na koncie usługi Automation, w ramach współbieżnego limitu zadań.

#### <a name="resolution"></a>Rozwiązanie

W przypadku zastosowania [grup dynamicznych](../automation-update-management.md#using-dynamic-groups) do wdrożeń aktualizacji.

* Sprawdź, czy maszyna nadal istnieje i jest osiągalna. Jeśli nie istnieje, Edytuj wdrożenie i Usuń maszynę.
* Zapoznaj się z sekcją [Planowanie w sieci](../automation-update-management.md#ports) , aby uzyskać listę portów i adresów wymaganych do Update Management i sprawdź, czy maszyna spełnia te wymagania.
* Uruchom następujące zapytanie w log Analytics, aby znaleźć maszyny w środowisku, których `SourceComputerId` zmieniono. Wyszukaj komputery mające taką samą `Computer` wartość, ale inną `SourceComputerId` wartość. Po znalezieniu odpowiednich maszyn należy zmodyfikować wdrożenia aktualizacji, które są przeznaczone dla tych maszyn, a następnie usunąć i ponownie dodać maszyny, aby `SourceComputerId` odzwierciedlały poprawną wartość.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

### <a name="hresult"></a>Scenariusz Komputer pokazuje jako nieoceniony i pokazuje wyjątek HResult

#### <a name="issue"></a>Problem

Masz maszyny, które pokazują, że **nie oceniono** pod **względem zgodności**, i zobaczysz komunikat o wyjątku poniżej.

#### <a name="cause"></a>Przyczyna

Windows Update lub WSUS nie są poprawnie skonfigurowane na maszynie. Update Management polega Windows Update lub WSUS w celu zapewnienia wymaganych aktualizacji, stanu poprawki oraz wyników wdrożonych poprawek. Bez tych informacji Update Management nie może poprawnie zgłosić poprawek wymaganych lub zainstalowanych.

#### <a name="resolution"></a>Rozwiązanie

Kliknij dwukrotnie wyjątek wyświetlany na czerwono, aby wyświetlić cały komunikat o wyjątku. Zapoznaj się z poniższą tabelą, aby uzyskać potencjalne rozwiązania lub akcje do wykonania:

|Wyjątek  |Rozwiązanie lub Akcja  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Przeszukaj odpowiedni kod błędu na [liście kodów błędów w usłudze Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) , aby znaleźć dodatkowe szczegóły dotyczące przyczyny wyjątku.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Te błędy są problemy z łącznością sieciową. Upewnij się, że komputer ma odpowiednią łączność sieciową do Update Management. Zapoznaj się z sekcją [Planowanie sieci](../automation-update-management.md#ports) , aby uzyskać listę wymaganych portów i adresów.        |
|`0x8024001E`| Operacja aktualizacji nie została ukończona, ponieważ trwa zamykanie usługi lub systemu.|
|`0x8024002E`| Usługa Windows Update jest wyłączona.|
|`0x8024402C`     | W przypadku korzystania z serwera programu WSUS upewnij się, że wartości rejestru dla `WUServer` i `WUStatusServer` w kluczu `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` rejestru mają prawidłowy serwer WSUS.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Upewnij się, że usługa Windows Update (wuauserv) jest uruchomiona i nie jest wyłączona.        |
|Każdy inny wyjątek ogólny     | Wykonaj wyszukiwanie w Internecie w poszukiwaniu możliwych rozwiązań i pracuj z lokalną pomocą techniczną IT.         |

`windowsupdate.log` Przeglądanie może pomóc w próbie ustalenia możliwej przyczyny. Aby uzyskać więcej informacji na temat odczytywania dziennika, zobacz [jak odczytać plik WindowsUpdate. log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Dodatkowo można pobrać i uruchomić narzędzie do [rozwiązywania problemów Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) , aby sprawdzić, czy występują problemy z Windows Update na komputerze.

> [!NOTE]
> [Windows Update narzędzie do rozwiązywania problemów](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) jest przeznaczone dla klientów z systemem Windows, ale działa również w systemie Windows Server.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Scenariusz: Nie można uruchomić przebiegu aktualizacji

#### <a name="issue"></a>Problem

Nie można uruchomić przebiegu aktualizacji na komputerze z systemem Linux.

#### <a name="cause"></a>Przyczyna

Hybrydowy proces roboczy systemu Linux jest w złej kondycji.

#### <a name="resolution"></a>Rozwiązanie

Utwórz kopię następującego pliku dziennika i Zachowaj ją w celu rozwiązywania problemów:

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Scenariusz: Przebieg aktualizacji zostanie uruchomiony, ale napotka błędy

#### <a name="issue"></a>Problem

Rozpocznie się przebieg aktualizacji, ale napotka błędy podczas przebiegu.

#### <a name="cause"></a>Przyczyna

Możliwe przyczyny:

* Menedżer pakietów jest w złej kondycji
* Określone pakiety mogą zakłócać stosowanie poprawek opartych na chmurze
* Inne przyczyny

#### <a name="resolution"></a>Rozwiązanie

Jeśli wystąpią błędy podczas przebiegu aktualizacji po pomyślnym uruchomieniu w systemie Linux, sprawdź dane wyjściowe zadania z komputera, którego dotyczy problem, w przebiegu. Niektóre komunikaty o błędach można znaleźć od Menedżera pakietów maszyny, które można zbadać i podjąć odpowiednie działania. Update Management wymaga, aby Menedżer pakietów był w dobrej kondycji w przypadku pomyślnych wdrożeń aktualizacji.

W niektórych przypadkach aktualizacje pakietów mogą zakłócać działanie Update Management uniemożliwiające ukończenie wdrożenia aktualizacji. Jeśli widzisz tę opcję, musisz wykluczyć te pakiety z przyszłych przebiegów aktualizacji lub ręcznie zainstalować je samodzielnie.

Jeśli nie możesz rozwiązać problemu z poprawkami, Utwórz kopię następującego pliku dziennika i Zachowaj ją **przed** rozpoczęciem następnego wdrożenia aktualizacji w celu rozwiązywania problemów:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-are-not-installed"></a>Nie zainstalowano poprawek

### <a name="machines-do-not-install-updates"></a>Na maszynach nie są instalowane aktualizacje

* Spróbuj uruchomić aktualizacje bezpośrednio na maszynie. Jeśli nie można zaktualizować maszyny, zapoznaj się z [listę potencjalnych błędów w przewodniku rozwiązywania problemów](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Jeśli aktualizacje są uruchamiane lokalnie, spróbuj usunąć i zainstalować ponownie agenta na maszynie, postępując zgodnie z instrukcjami w artykule [„Usuwanie maszyny wirtualnej z rozwiązania Update Management”](https://docs.microsoft.com/azure/automation/automation-update-management#remove-a-vm-for-update-management).

### <a name="i-know-updates-are-available-but-they-dont-show-as-needed-on-my-machines"></a>Wiemy, że aktualizacje są dostępne, ale nie są wyświetlane w razie konieczności na maszynach

* Często dzieje się tak, gdy maszyny zostały skonfigurowane do pobierania aktualizacji z usług WSUS/programu SCCM, ale aktualizacje nie zostały zatwierdzone przez usługi WSUS/program SCCM.
* Możesz sprawdzić, czy maszyny zostały skonfigurowane na potrzeby usług WSUS/programu SCCM, [korzystając z odsyłacza klucza rejestru „UseWUServer” do kluczy rejestru w sekcji „Configuring Automatic Updates by Editing the Registry” (Konfigurowanie aktualizacji automatycznych przez edytowanie rejestru) tego dokumentu](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>**Aktualizacje są wyświetlane jako zainstalowane, ale nie mogę ich znaleźć na swojej maszynie**

* Aktualizacje są często zastępowane przez inne aktualizacje. Aby uzyskać więcej informacji, zobacz [część „Update is superseded” (Aktualizacja została zastąpiona) w przewodniku Windows Update Troubleshooting (Rozwiązywanie problemów z witryną Windows Update)](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)

### <a name="installing-updates-by-classification-on-linux"></a>**Instalowanie aktualizacji według klasyfikacji w systemie Linux**

* Wdrażanie aktualizacji w systemie Linux według klasyfikacji („Aktualizacje krytyczne i zabezpieczeń”) ma ważne zastrzeżenia, szczególnie w przypadku systemu CentOS. Te [ograniczenia zostały opisane na stronie omówienia rozwiązania Update Management](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)

### <a name="kb2267602-is-consistently--missing"></a>**Stale brakuje aktualizacji KB2267602**

* KB2267602 to [aktualizacja definicji usługi Windows Defender](https://www.microsoft.com/wdsi/definitions). Jest ona aktualizowana codziennie.

## <a name="other"></a>Scenariusz Mój problem nie jest wymieniony powyżej

### <a name="issue"></a>Problem

Wystąpił problem, który nie został rozwiązany przez inne scenariusze wymienione poniżej.

### <a name="cause"></a>Przyczyna

Błędna konfiguracja lub brak kluczy rejestru może powodować problemy z Update Management.

### <a name="resolution"></a>Rozwiązanie

Usuń klucz `HKLM:\SOFTWARE\Microsoft\HybridRunbookWorker` rejestru i uruchom ponownie **HealthService**.

Można również użyć następujących poleceń programu PowerShell.

```powershell
Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
Restart-Service healthservice
```

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
