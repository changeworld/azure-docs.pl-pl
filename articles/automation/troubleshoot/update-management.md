---
title: Rozwiązywanie problemów z zarządzaniem aktualizacjami
description: Dowiedz się, jak rozwiązywać problemy związane z zarządzaniem aktualizacjami
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 9bcc871ecc9413f02545e6aec4caa6342d563b44
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66474574"
---
# <a name="troubleshooting-issues-with-update-management"></a>Rozwiązywanie problemów z zarządzaniem aktualizacjami

W tym artykule omówiono rozwiązania, aby rozwiązać problemy, które może działać w przypadku korzystania z rozwiązania Update Management.

Brak agenta narzędzia do rozwiązywania problemów dla agenta hybrydowego procesu roboczego, aby określić pierwotny problem. Aby dowiedzieć się więcej na temat rozwiązywania problemów, zobacz [problemy dotyczące rozwiązywania problemów aktualizacji agenta](update-agent-issues.md). W przypadku pozostałych problemów Zobacz szczegółowe informacje poniżej o możliwych problemach.

## <a name="general"></a>Ogólne

### <a name="components-enabled-not-working"></a>Scenariusz: Składniki rozwiązania "Update Management" zostały włączone i teraz jest konfigurowany tej maszyny wirtualnej

#### <a name="issue"></a>Problem

Nadal wyświetlony następujący komunikat na maszynie wirtualnej w ciągu 15 minut po dołączeniu:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany przez następujących przyczyn:

1. Konto usługi Automation komunikacja jest blokowana.
2. Maszyna wirtualna jest dołączona może pochodzić z klonowanym komputerze, który nie był Sysprep przy użyciu programu Microsoft Monitoring Agent zainstalowany.

#### <a name="resolution"></a>Rozwiązanie

1. Odwiedź stronę, [Planowanie sieci](../automation-hybrid-runbook-worker.md#network-planning) Aby dowiedzieć się więcej o tym, jakie adresy i porty muszą być dozwolone do zarządzania aktualizacjami do pracy.
2. Jeśli przy użyciu sklonowanego obrazu:
   1. W obszarze roboczym usługi Log Analytics, należy usunąć maszynę Wirtualną z zapisanego wyszukiwania dla konfiguracji zakresu `MicrosoftDefaultScopeConfig-Updates` Jeśli zostanie on wyświetlony. Zapisane wyszukiwania można znaleźć w obszarze **ogólne** w obszarze roboczym.
   2. Uruchom polecenie `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. Uruchom `Restart-Service HealthService` ponownego uruchomienia `HealthService`. Spowoduje to ponowne utworzenie klucza i generowanie nowego identyfikatora UUID.
   4. Jeśli to nie rozwiąże problemu, obraz sysprep pierwszy i zainstalować agenta MMA po fakcie.

### <a name="multi-tenant"></a>Scenariusz: Otrzymujesz błąd połączonej subskrypcji podczas tworzenia wdrożenia aktualizacji dla maszyn w innej dzierżawie platformy Azure.

#### <a name="issue"></a>Problem

Pojawi się następujący błąd podczas próby utworzenia wdrożenia aktualizacji dla maszyn w innej dzierżawie platformy Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas tworzenia wdrożenia aktualizacji, który ma maszyn wirtualnych platformy Azure w innej dzierżawie uwzględnione we wdrożeniu aktualizacji.

#### <a name="resolution"></a>Rozwiązanie

Należy użyć następującego obejścia można pobrać je według harmonogramu. Możesz użyć [polecenia New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) polecenia cmdlet z przełącznikiem `-ForUpdate` Tworzenie harmonogramu i używanie [New AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) polecenia cmdlet i przekaż maszyny w innej dzierżawie do `-NonAzureComputer` parametru. Poniższy przykład przedstawia przykład, jak to zrobić:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>Scenariusz: Maszyny nie pojawiają się w portalu w obszarze Zarządzanie aktualizacjami

#### <a name="issue"></a>Problem

Można uruchamiać w następujących scenariuszach:

* Programy telewizyjne maszyny **nieskonfigurowane** w widoku Zarządzanie aktualizacjami maszyny wirtualnej

* Maszyny są nieobecne w widoku zarządzania aktualizacjami konta usługi Automation

* Masz maszyny, które pokazują, jak **nie oceniono** w obszarze **zgodności**, widzą danych pulsu w dziennikach usługi Azure Monitor do hybrydowego procesu roboczego Runbook, ale nie do zarządzania aktualizacjami.

#### <a name="cause"></a>Przyczyna

Może to być spowodowane przez potencjalne problemy z konfiguracją lokalnego lub nieprawidłowo skonfigurowane konfiguracji zakresu.

Hybrydowy proces roboczy elementu Runbook może być konieczne może być ponownie zarejestrowane i ponowna instalacja.

W obszarze roboczym, do której dane osiągnął i zatrzymywanie zapisywanie zostały zdefiniowane limit przydziału.

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że na swojej maszynie wysyła raporty do prawidłowym obszarem roboczym. Sprawdź, jakie obszar roboczy raporty do komputera. Aby uzyskać instrukcje na temat to sprawdzić, zobacz [sprawdzić połączenie agenta z usługą Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Następnie upewnij się, że to jest obszar roboczy, który jest połączony z Twoim kontem usługi Azure Automation. Aby to sprawdzić, przejdź do swojego konta usługi Automation, a następnie kliknij przycisk **połączony obszar roboczy** w obszarze **powiązane zasoby**.

* Sprawdź maszyn pojawiają się w obszarze roboczym usługi Log Analytics. Uruchom następujące zapytanie w obszarze roboczym usługi Log Analytics jest połączony z Twoim kontem usługi Automation. Jeśli nie widzisz swojej maszynie w wynikach zapytania, ten komputer nie jest przesyłanie interwałów pulsu, co oznacza, że prawdopodobnie występuje problem z konfiguracją lokalnego. Możesz uruchomić narzędzie do rozwiązywania problemów dla [Windows](update-agent-issues.md#troubleshoot-offline) lub [Linux](update-agent-issues-linux.md#troubleshoot-offline) w zależności od systemu operacyjnego, lub można [ponownie zainstalować agenta](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Jeśli komputer zostaną wyświetlone w wynikach zapytania, a następnie należy bardzo konfiguracji zakresu określonego w następny.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* Sprawdź, czy problemy z konfiguracją zakresu. [Zakres konfiguracji](../automation-onboard-solutions-from-automation-account.md#scope-configuration) określa Konfigurowanie maszyn dla rozwiązania. Jeśli komputer jest widoczna w obszarze roboczym, ale nie jest wyświetlane, użytkownik będzie musiał skonfigurować konfiguracji zakresu pod kątem maszyn. Aby dowiedzieć się, jak to zrobić, zobacz [dołączać maszyny w obszarze roboczym](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Jeśli powyższe czynności nie rozwiąże problemu, wykonaj czynności opisane w temacie [wdrożenia Windows hybrydowego procesu roboczego Runbook](../automation-windows-hrw-install.md) ponowna instalacja hybrydowego procesu roboczego dla Windows lub [wdrożenia procesu roboczego elementu Runbook dla hybrydowych w systemie Linux](../automation-linux-hrw-install.md) dla systemu Linux.

* W obszarze roboczym uruchom następujące zapytanie. Jeśli zostanie wyświetlony wynik `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` mają przydział zdefiniowane w obszarze roboczym usługi zostanie osiągnięty, która została zatrzymana, dane są zapisywane. W obszarze roboczym, przejdź do **użycie i szacunkowe koszty** > **zarządzanie ilością danych** i sprawdź limitu przydziału lub Usuń limit przydziału, masz.

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

## <a name="windows"></a>Windows

Jeśli wystąpią problemy podczas próby dołączyć rozwiązanie na maszynie wirtualnej, sprawdź **programu Operations Manager** zdarzeń dziennika **Dzienniki aplikacji i usług** na maszynę lokalną pod kątem zdarzeń za pomocą Identyfikator zdarzenia **4502** i komunikat zdarzenia zawierający **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

Poniższa sekcja wyróżnia określone komunikaty o błędach i możliwe rozwiązanie dla każdego. Dla dołączania innych problemów znajduje się pozycja [Rozwiązywanie problemów z dołączania rozwiązań](onboarding.md).

### <a name="machine-already-registered"></a>Scenariusz: Maszyna jest już zarejestrowana dla innego konta

#### <a name="issue"></a>Problem

Pojawi się następujący komunikat o błędzie:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Przyczyna

Komputer jest już dołączona do innego obszaru roboczego na potrzeby zarządzania aktualizacjami.

#### <a name="resolution"></a>Rozwiązanie

Przeprowadź czyszczenie starych artefaktów na komputerze przez [usuwanie grupę hybrydowych elementów runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) , a następnie spróbuj ponownie.

### <a name="machine-unable-to-communicate"></a>Scenariusz: Maszyna nie jest w stanie nawiązać połączenia z usługą

#### <a name="issue"></a>Problem

Wyświetlany jest jeden z następujących komunikatów o błędzie:

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

Może to być serwer proxy, bramy lub zapora blokuje komunikację sieciową.

#### <a name="resolution"></a>Rozwiązanie

Przejrzyj sieci i upewnij się, że odpowiednie porty i adresy są dozwolone. Zobacz [wymagania dotyczące sieciowej](../automation-hybrid-runbook-worker.md#network-planning), aby uzyskać listę portów i adresów, które są wymagane przez rozwiązanie Update Management i hybrydowych procesów roboczych Runbook.

### <a name="unable-to-create-selfsigned-cert"></a>Scenariusz: Nie można utworzyć certyfikatu z podpisem własnym

#### <a name="issue"></a>Problem

Wyświetlany jest jeden z następujących komunikatów o błędzie:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Przyczyna

Hybrydowy proces roboczy elementu Runbook nie był w stanie wygenerować certyfikat z podpisem własnym

#### <a name="resolution"></a>Rozwiązanie

Sprawdź konto systemowe ma dostęp do odczytu do folderu **C:\ProgramData\Microsoft\Crypto\RSA** , a następnie spróbuj ponownie.

### <a name="failed-to-start"></a>Scenariusz: Pokazuje maszynę, nie można uruchomić w wdrożenia aktualizacji

#### <a name="issue"></a>Problem

Maszyna ma stan **udało się uruchomić** dla maszyny. Wyświetlając szczegółowe informacje dla maszyny zostanie wyświetlony następujący błąd:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>Przyczyna

Ten błąd może się zdarzyć z jednego z następujących powodów:

* Maszyny już nie istnieje.
* Maszyny jest włączona, wyłączona i jest nieosiągalny.
* Ta maszyna ma problem z łącznością sieciową i hybrydowego procesu roboczego na maszynie jest nieosiągalny.
* Podczas aktualizacji do programu Microsoft Monitoring Agent, zmienić SourceComputerId
* Przebiegu aktualizacji może zostały ograniczone, jeśli osiągnięty limit 2000 równoczesnych zadań w ramach konta usługi Automation. Każde wdrożenie jest uważane za zadanie i każdym komputerze w liczba wdrożeń aktualizacji zadania. Wszystkich innych automatyzacji zadań lub aktualizacji wdrożeń aktualnie uruchomione w liczba Twoich konta usługi Automation w kierunku limit współbieżnych zadań.

#### <a name="resolution"></a>Rozwiązanie

Gdy użycie odpowiednich [grup dynamicznych](../automation-update-management.md#using-dynamic-groups) wdrożeń aktualizacji.

* Sprawdź komputer nadal istnieje i jest osiągalny. Jeśli nie istnieje, Edytuj wdrożenie i Usuń maszynę.
* Zobacz sekcję dotyczącą [Planowanie sieci](../automation-update-management.md#ports) lista portów i adresów, które są wymagane do zarządzania aktualizacjami i sprawdź Twoja maszyna spełnia te wymagania.
* Uruchom następujące zapytanie w usłudze Log Analytics można znaleźć maszyny w środowisku którego `SourceComputerId` zmienione. Wyszukaj komputery, które mają taki sam `Computer` wartość, ale o innej `SourceComputerId` wartość. Po znalezieniu wykorzystywanych maszyn, należy edytować wdrożenia aktualizacji, które docelowych tych maszyn i Usuń i ponownie Dodaj maszyny więc `SourceComputerId` odzwierciedla poprawnej wartości.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

### <a name="hresult"></a>Scenariusz: Maszyna jest wyświetlana jako nie oceniono i przedstawia wyjątek HResult

#### <a name="issue"></a>Problem

Masz maszyny, które pokazują, jak **nie oceniono** w obszarze **zgodności**, i zostanie wyświetlony komunikat o wyjątku poniżej.

#### <a name="cause"></a>Przyczyna

Windows Update lub WSUS nie jest skonfigurowany prawidłowo na maszynie. Rozwiązanie Update Management korzysta z witryny Windows Update lub WSUS w celu udostępnienia aktualizacji, które są potrzebne, stan poprawki i wyniki poprawki wdrożone. Bez tych informacji rozwiązania Update Management nie może poprawnie raport na temat poprawek, które są wymagane lub zainstalowane.

#### <a name="resolution"></a>Rozwiązanie

Kliknij dwukrotnie wyjątek wyświetlane na czerwono, aby wyświetlić komunikat o wyjątku całego. Przejrzyj poniższą tabelę, potencjalne rozwiązania lub akcje do wykonania:

|Wyjątek  |Rozdzielczość lub Akcja  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Wyszukiwanie kodu błędów w [Windows zaktualizować lista kodów błędów](https://support.microsoft.com/help/938205/windows-update-error-code-list) można znaleźć dodatkowe szczegóły na przyczyną wyjątku.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Takie błędy występują problemy z połączeniem sieciowym. Upewnij się, że Twoja maszyna ma zapewniona odpowiednia łączność sieciowa do zarządzania aktualizacjami. Zobacz sekcję dotyczącą [Planowanie sieci](../automation-update-management.md#ports) lista portów i adresów, które są wymagane.        |
|`0x8024001E`| Operacja aktualizacji nie została ukończona, ponieważ usługi lub systemu była zamknięta.|
|`0x8024002E`| Usługa Windows Update jest wyłączona.|
|`0x8024402C`     | Jeśli używasz serwera programu WSUS, upewnij się, wartości rejestru `WUServer` i `WUStatusServer` w kluczu rejestru `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` prawidłowy serwer WSUS.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Upewnij się, że usługa Windows Update (wuauserv) jest uruchomiony, a nie jest wyłączona.        |
|Inne wyjątek ogólny     | Przeprowadź wyszukiwanie internet w poszukiwaniu możliwych rozwiązań i pracować z lokalnej pomocy technicznej IT.         |

Przeglądanie `windowsupdate.log` może pomóc ustalić, jak również możliwą przyczyną. Aby uzyskać więcej informacji na temat sposobu odczytywania dziennika, zobacz [sposób odczytywania pliku Windowsupdate.log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Dodatkowo można pobierać i uruchamiać [Windows Update do rozwiązywania problemów z](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) do sprawdzenia, jeśli występują problemy z usługą Windows Update na maszynie.

> [!NOTE]
> [Windows Update do rozwiązywania problemów z](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) stany, jest on przeznaczony dla klientów Windows, ale także działa w systemie Windows Server.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Scenariusz: Przebieg aktualizacji zakończy się niepowodzeniem rozpocząć

#### <a name="issue"></a>Problem

Niepowodzenie uruchomienia aktualizacji można uruchomić na maszynie z systemem Linux.

#### <a name="cause"></a>Przyczyna

Hybrydowy proces roboczy Linux jest w złej kondycji.

#### <a name="resolution"></a>Rozwiązanie

Utwórz kopię w następującym pliku dziennika i ją zachować na potrzeby rozwiązywania problemów:

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Scenariusz: Przebieg aktualizacji uruchamiany, jednak wystąpią błędy

#### <a name="issue"></a>Problem

Uruchamia się podczas przebiegu aktualizacji, ale napotka błędy podczas uruchamiania.

#### <a name="cause"></a>Przyczyna

Może być możliwe przyczyny:

* Menedżer pakietów jest w złej kondycji
* Określone pakiety może zakłócać poprawek oparte na chmurze
* Inne przyczyny

#### <a name="resolution"></a>Rozwiązanie

Jeśli wystąpią błędy podczas aktualizacji uruchomienia po pomyślnym uruchomieniu w systemie Linux, sprawdź zadanie danych wyjściowych z zainfekowanego komputera w przebiegu. Z Menedżera pakietu na komputerze, który można zbadać i podejmować działania w może się okazać określone komunikaty o błędach. Rozwiązanie Update Management wymaga Menedżera pakietów, będące w dobrej kondycji, w przypadku wdrożeń Pomyślna aktualizacja.

W niektórych przypadkach aktualizacjami pakietów może zakłócać zarządzania aktualizacjami, co uniemożliwia ukończenie wdrożenia aktualizacji. Jeśli zobaczysz, że, musisz wykluczyć te pakiety z przebiegów w przyszłej aktualizacji albo ręcznie zainstalować je samodzielnie.

Jeśli nie możesz rozwiązać problem z poprawką, Utwórz kopię w następującym pliku dziennika i ją zachować **przed** następnego wdrażania aktualizacji, który rozpoczyna się na potrzeby rozwiązywania problemów:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie był widoczny problemu lub są w stanie rozwiązać problemu, odwiedź jedną z następujących kanałów obsługi więcej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.
