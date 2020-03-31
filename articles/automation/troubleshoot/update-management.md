---
title: Rozwiązywanie problemów z błędami za pomocą usługi Azure Update Management
description: Dowiedz się, jak rozwiązywać problemy i rozwiązywać problemy z rozwiązaniem zarządzania aktualizacjami na platformie Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 91bcdc65a7ff3bcaf09f12d69ba4c7aaeb84ffa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132858"
---
# <a name="troubleshooting-issues-with-update-management"></a>Rozwiązywanie problemów z zarządzaniem aktualizacjami

W tym artykule omówiono rozwiązania problemów, które mogą wystąpić podczas korzystania z zarządzania aktualizacjami.

Istnieje narzędzie do rozwiązywania problemów z agentem agenta hybrydowego procesu roboczego w celu określenia podstawowego problemu. Aby dowiedzieć się więcej o narzędziu do rozwiązywania problemów, zobacz [Rozwiązywanie problemów z agentem aktualizacji](update-agent-issues.md). W przypadku wszystkich innych problemów należy użyć następujących wskazówek dotyczących rozwiązywania problemów.

Jeśli wystąpią problemy podczas próby dołączania rozwiązania na maszynie wirtualnej (VM), sprawdź dziennik **programu Operations Manager** w obszarze Dzienniki aplikacji i usług na komputerze lokalnym, aby uzyskać informacje o **zdarzeniach** o identyfikatorze zdarzenia 4502 i szczegółach zdarzenia. `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`

W poniższej sekcji przedstawiono konkretne komunikaty o błędach i możliwe rozwiązania dla każdego z nich. Aby uzyskać inne problemy z dołączaniem, zobacz [Rozwiązywanie problemów z dołączaniem](onboarding.md)do rozwiązania .

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>Scenariusz: Pojawia się błąd "Nie można włączyć rozwiązania aktualizacji"

### <a name="issue"></a>Problem

Podczas próby włączenia rozwiązania do zarządzania aktualizacjami na koncie automatyzacji pojawia się następujący błąd:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić z następujących powodów:

* Wymagania zapory sieciowej agenta usługi Log Analytics mogą nie być poprawnie skonfigurowane, co powoduje niepowodzenie agenta podczas rozpoznawania adresów URL DNS.

* Kierowanie na rozwiązania jest nieprawidłowo skonfigurowane, a komputer nie otrzymuje aktualizacji zgodnie z oczekiwaniami.

* Można również zauważyć, że urządzenie `Non-compliant` pokazuje stan w obszarze **Zgodność**. W tym samym czasie **gotowość aktualizacji agenta** zgłasza agenta jako `Disconnected`.

### <a name="resolution"></a>Rozwiązanie

* Uruchom narzędzie do rozwiązywania problemów dla [systemu Windows](update-agent-issues.md#troubleshoot-offline) lub [Linux](update-agent-issues-linux.md#troubleshoot-offline), w zależności od systemu operacyjnego.

* Przejdź do [witryny Planowanie sieci,](../automation-hybrid-runbook-worker.md#network-planning) aby dowiedzieć się, które adresy i porty muszą mieć możliwość działania zarządzania aktualizacjami.  

* Przejdź do [usługi Planowanie sieci,](../../azure-monitor/platform/log-analytics-agent.md#network-requirements) aby dowiedzieć się, które adresy i porty muszą być dozwolone dla agenta usługi Log Analytics do pracy.

* Sprawdź, czy nie występują problemy z konfiguracją zakresu. [Konfiguracja zakresu](../automation-onboard-solutions-from-automation-account.md#scope-configuration) określa, które maszyny są skonfigurowane dla rozwiązania. Jeśli komputer jest wyświetlany w obszarze roboczym, ale nie w portalu **zarządzania aktualizacjami,** należy ustawić konfigurację zakresu, aby kierować na maszyny. Aby dowiedzieć się więcej o konfiguracji zakresu, zobacz [Wbudowane maszyny w obszarze roboczym](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Usuń konfigurację procesu roboczego, wykonując kroki opisane w [ustępuje usunięcie hybrydowego procesu roboczego elementów runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker). 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Scenariusz: Zastąpiona aktualizacja wskazana jako brak owana w zarządzaniu aktualizacjami

### <a name="issue"></a>Problem

Stare aktualizacje są wyświetlane w zarządzanie aktualizacjami na koncie automatyzacji jako brakujące, nawet jeśli zostały zastąpione. Zastąpiona aktualizacja to aktualizacja, która nie musi być zainstalowana, ponieważ dostępna jest późniejsza aktualizacja, która koryguje tę samą lukę. Zarządzanie aktualizacjami ignoruje zastąpiona aktualizacja i sprawia, że nie ma ona zastosowania na rzecz aktualizacji zastępującej. Aby uzyskać informacje o powiązanym problemie, zobacz [Aktualizacja została zastąpiona](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Przyczyna

Zastąpione aktualizacje nie są poprawnie wskazywane jako odrzucone, dzięki czemu można je uznać za nie dotyczy.

### <a name="resolution"></a>Rozwiązanie

Gdy zastąpiona aktualizacja staje się w 100 procentach nie dotyczy, `Declined`należy zmienić stan zatwierdzenia tej aktualizacji na . Aby to zrobić dla wszystkich aktualizacji:

1. Na koncie Automatyzacja wybierz pozycję **Zarządzanie aktualizacjami,** aby wyświetlić stan komputera. Zobacz [Wyświetlanie ocen aktualizacji](../manage-update-multi.md#view-an-update-assessment).

2. Sprawdź zastąpiona aktualizacja, aby upewnić się, że nie ma ona zastosowania w 100 procentach. 

3. Oznacz aktualizację jako odrzuconą, chyba że masz pytanie dotyczące aktualizacji. 

4. Wybierz **pozycję Komputery** i w kolumnie **Zgodność** wymusz ponownie przeskanowanie w celu zapewnienia zgodności. Zobacz [Zarządzanie aktualizacjami dla wielu komputerów](../manage-update-multi.md).

5. Powtórz powyższe kroki, aby uzyskać inne zastąpione aktualizacje.

6. Uruchom kreatora oczyszczania, aby usunąć pliki z odrzuconych aktualizacji. 

7. W przypadku programu WSUS należy ręcznie wyczyścić wszystkie zastąpione aktualizacje, aby odświeżyć infrastrukturę.

8. Regularnie powtarzaj tę procedurę, aby rozwiązać problem z wyświetlaniem i zminimalizować ilość miejsca na dysku używanego do zarządzania aktualizacjami.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Scenariusz: maszyny nie są wyświetlane w portalu w obszarze Zarządzanie aktualizacjami

### <a name="issue"></a>Problem

Urządzenia mają następujące objawy:

* Komputer jest `Not configured` wyświetlany w widoku Zarządzanie aktualizacjami maszyny Wirtualnej.

* Brak maszyn w widoku Zarządzanie aktualizacjami konta usługi Azure Automation.

* Masz maszyny, które `Not assessed` pokazują się jako w ramach **zgodności**. Jednak dane pulsu w dziennikach usługi Azure Monitor dla procesu roboczego hybrydowego systemu runbook, ale nie dla zarządzania aktualizacjami.

### <a name="cause"></a>Przyczyna

Ten problem może być spowodowany przez lokalne problemy z konfiguracją lub nieprawidłowo skonfigurowane konfiguracji zakresu.

Może być trzeba ponownie zarejestrować i ponownie zainstalować hybrydowy proces roboczy uruchomieniu. ścieżek.

Być może zdefiniowano przydział w obszarze roboczym, który został osiągnięty i uniemożliwia dalsze przechowywanie danych.

### <a name="resolution"></a>Rozwiązanie

* Uruchom narzędzie do rozwiązywania problemów dla [systemu Windows](update-agent-issues.md#troubleshoot-offline) lub [Linux](update-agent-issues-linux.md#troubleshoot-offline), w zależności od systemu operacyjnego.

* Upewnij się, że komputer zgłasza się do właściwego obszaru roboczego. Aby uzyskać wskazówki dotyczące weryfikowania tego [aspektu, zobacz Weryfikowanie łączności agenta z usługi Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Upewnij się również, że ten obszar roboczy jest połączony z kontem usługi Azure Automation. Aby potwierdzić, przejdź do konta automatyzacji i wybierz pozycję **Połączony obszar roboczy** w obszarze **Zasoby pokrewne**.

* Upewnij się, że maszyny są wyświetlane w obszarze roboczym usługi Log Analytics. Uruchom następującą kwerendę w obszarze roboczym usługi Log Analytics, która jest połączona z kontem automatyzacji:

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
 
  Jeśli komputer nie jest widoczny w wynikach kwerendy, nie został ostatnio zaewidencjonowany, co oznacza, że prawdopodobnie występuje problem z konfiguracją lokalną i należy [ponownie zainstalować agenta.](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows) Jeśli komputer pojawia się w wynikach kwerendy, należy sprawdzić konfigurację zakresu określoną w następnym elementem punktowanym na tej liście.

* Sprawdź, czy nie występują problemy z konfiguracją zakresu. [Konfiguracja zakresu](../automation-onboard-solutions-from-automation-account.md#scope-configuration) określa, które maszyny są skonfigurowane dla rozwiązania. Jeśli komputer jest wyświetlany w obszarze roboczym, ale nie w portalu **zarządzania aktualizacjami,** należy skonfigurować konfigurację zakresu do kierowania na maszyny. Aby dowiedzieć się, jak to zrobić, zobacz [Wbudowane maszyny w obszarze roboczym](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* W obszarze roboczym uruchom następującą kwerendę:

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

  Jeśli uzyskasz `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` wynik, w obszarze roboczym zdefiniowano przydział, który został osiągnięty i który uniemożliwił zapisanie danych. W obszarze roboczym przejdź do **obszaru użytkowania i szacowanych kosztów** > **zarządzania woluminami danych** i sprawdź przydział lub usuń go.

* Jeśli te kroki nie rozwiążą problemu, wykonaj kroki opisane w [programie Wdrażanie hybrydowego procesu roboczego systemu Windows](../automation-windows-hrw-install.md) w celu ponownego zainstalowania procesu roboczego hybrydowego dla systemu Windows. W przypadku systemu Linux wykonaj kroki opisane w sekcji [Wdrażanie procesu roboczego hybrydowego systemu Linux](../automation-linux-hrw-install.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenariusz: Nie można zarejestrować dostawcy zasobów automatyzacji dla subskrypcji

### <a name="issue"></a>Problem

Podczas pracy z rozwiązaniami na koncie automatyzacji pojawia się następujący błąd:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Przyczyna

Dostawca zasobów automatyzacji nie jest zarejestrowany w subskrypcji.

### <a name="resolution"></a>Rozwiązanie

Aby zarejestrować dostawcę zasobów automatyzacji, wykonaj następujące kroki w witrynie Azure portal:

1. Na liście usługi platformy Azure u dołu portalu wybierz pozycję **Wszystkie usługi**, a następnie wybierz **pozycję Subskrypcje** w grupie Usługa ogólne.
2. Wybierz subskrypcję.
3. W obszarze **Ustawienia**wybierz pozycję **Dostawcy zasobów**.
4. Z listy dostawców zasobów sprawdź, `Microsoft.Automation` czy dostawca zasobów jest zarejestrowany.
5. Jeśli nie ma go na `Microsoft.Automation` liście, zarejestruj dostawcę, wykonując kroki opisane w [urzędzie rozpoznawania błędów dla rejestracji dostawcy zasobów](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Scenariusz: Składniki dla rozwiązania zarządzania aktualizacjami włączone, podczas gdy maszyna wirtualna nadal jest pokazywany jako skonfigurowany

### <a name="issue"></a>Problem

Po 15 minutach od dołączania na maszynie wirtualnej jest wyświetlany następujący komunikat:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić z następujących powodów:

* Komunikacja z kontem automatyzacji jest blokowana.

* Istnieje zduplikowana nazwa komputera z różnymi identyfikatorami komputerów źródłowych. W tym scenariuszu występuje, gdy maszyna wirtualna o określonej nazwie komputera jest tworzona w różnych grupach zasobów i raportuje do tego samego obszaru roboczego agenta logistycznego w subskrypcji.

* Obraz maszyny Wirtualnej jest wbudowany może pochodzić z sklonowanego komputera, który nie został przygotowany z przygotowaniem systemu (sysprep) z zainstalowanym agentem monitorowania firmy Microsoft (MMA).

### <a name="resolution"></a>Rozwiązanie

Aby pomóc w określeniu dokładnego problemu z maszyną wirtualną, uruchom następującą kwerendę w obszarze roboczym usługi Log Analytics, która jest połączona z kontem automatyzacji:

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Zablokowano komunikację z kontem automatyzacji

Przejdź do [witryny Planowanie sieci,](../automation-update-management.md#ports) aby dowiedzieć się, które adresy i porty muszą mieć możliwość działania zarządzania aktualizacjami.

#### <a name="duplicate-computer-name"></a>Zduplikowana nazwa komputera

Zmień nazwę maszyn wirtualnych, aby zapewnić unikatowe nazwy w ich środowisku.

#### <a name="onboarded-image-from-cloned-machine"></a>Wbudowany obraz ze sklonowanej maszyny

Jeśli używasz sklonowanego obrazu, różne nazwy komputerów mają ten sam identyfikator komputera źródłowego. W takim przypadku:

1. W obszarze roboczym usługi Log Analytics usuń maszynę `MicrosoftDefaultScopeConfig-Updates` wirtualną z zapisanego wyszukiwania konfiguracji zakresu, jeśli jest wyświetlana. Zapisane wyszukiwania można znaleźć w obszarze **Ogólne** w obszarze roboczym.

2. Uruchom następujące polecenie cmdlet:

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Uruchom, `Restart-Service HealthService` aby ponownie uruchomić usługę kondycji. Ta operacja odtwarza klucz i generuje nowy UUID.

4. Jeśli to podejście nie działa, najpierw uruchom program sysprep na obrazie, a następnie zainstaluj program MMA.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Scenariusz: Podczas tworzenia wdrożenia aktualizacji dla komputerów w innej dzierżawie platformy Azure pojawia się błąd połączonej subskrypcji

### <a name="issue"></a>Problem

Podczas próby utworzenia wdrożenia aktualizacji dla komputerów w innej dzierżawie platformy Azure wystąpi następujący błąd:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas tworzenia wdrożenia aktualizacji, które ma maszyny wirtualne platformy Azure w innej dzierżawie, która jest uwzględniona we wdrożeniu aktualizacji.

### <a name="resolution"></a>Rozwiązanie

Użyj następującego obejścia, aby zaplanować te elementy. Aby utworzyć harmonogram, można użyć polecenia cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) z `ForUpdate` parametrem. Następnie użyj polecenia cmdlet [konfiguracji New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) i przekaż `NonAzureComputer` maszyny w innej dzierżawie do parametru. W poniższym przykładzie pokazano, jak to zrobić:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Scenariusz: Niewyjaśnione ponowne uruchomienie

### <a name="issue"></a>Problem

Mimo że ustawiono opcję **Kontrola ponownego uruchamiania** na **Nigdy nie uruchamiaj ponownie,** komputery są nadal ponownie uruchamiane po zainstalowaniu aktualizacji.

### <a name="cause"></a>Przyczyna

Usługa Windows Update może być modyfikowana przez kilka kluczy rejestru, z których każdy może modyfikować zachowanie ponownego uruchomienia.

### <a name="resolution"></a>Rozwiązanie

Przejrzyj klucze rejestru wymienione w obszarze [Konfigurowanie aktualizacji automatycznych, edytując klucze rejestru](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) i [rejestru używane do zarządzania ponownym uruchomieniem,](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) aby upewnić się, że maszyny są poprawnie skonfigurowane.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Scenariusz: Komputer pokazuje "Nie można uruchomić" we wdrożeniu aktualizacji

### <a name="issue"></a>Problem

Urządzenie pokazuje `Failed to start` stan. Podczas wyświetlania określonych szczegółów dla komputera pojawia się następujący błąd:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Przyczyna

Ten błąd może mieć jedną z następujących przyczyn:

* Maszyna już nie istnieje.
* Urządzenie jest wyłączone i nieosiągalne.
* Komputer ma problem z łącznością sieciową, a zatem hybrydowy pracownik na komputerze jest nieosiągalny.
* Nastąpiła aktualizacja programu MMA, która zmieniła identyfikator komputera źródłowego.
* Przebieg aktualizacji został ograniczony, jeśli osiągniesz limit 2000 równoczesnych zadań na koncie automatyzacji. Każde wdrożenie jest uważane za zadanie, a każdy komputer we wdrożeniu aktualizacji liczy się jako zadanie. Każde inne zadanie automatyzacji lub wdrożenie aktualizacji aktualnie uruchomione na koncie automatyzacji jest wliczane do limitu równoczesnych zadań.

### <a name="resolution"></a>Rozwiązanie

W stosownych przypadkach należy użyć [grup dynamicznych](../automation-update-management-groups.md) dla wdrożeń aktualizacji. Dodatkowo:

* Sprawdź, czy urządzenie nadal istnieje i jest osiągalne. 
* Jeśli komputer nie istnieje, edytuj wdrożenie i usuń komputer.
* Zobacz sekcję [planowania sieci,](../automation-update-management.md#ports) aby uzyskać listę portów i adresów wymaganych do zarządzania aktualizacjami, a następnie sprawdź, czy komputer spełnia te wymagania.
* Sprawdź łączność z hybrydowym pracownikiem roboczym żyjącego niu jednakowego, korzystając z narzędzia do rozwiązywania problemów z agentem hybrydowego agenta roboczego. Aby dowiedzieć się więcej o narzędziu do rozwiązywania problemów, zobacz [Rozwiązywanie problemów z agentem aktualizacji](update-agent-issues.md).
* Uruchom następującą kwerendę w usłudze Log Analytics, aby znaleźć maszyny w środowisku, dla którego zmienił się identyfikator komputera źródłowego. Poszukaj komputerów, które mają tę samą `Computer` wartość, ale inną `SourceComputerId` wartość.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

* Po znalezieniu komputerów, których dotyczy problem, edytuj wdrożenia aktualizacji przeznaczone dla tych `SourceComputerId` komputerów, a następnie usuń je i ponownie dodaj, aby odzwierciedlały poprawną wartość.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Scenariusz: Aktualizacje są instalowane bez wdrożenia

### <a name="issue"></a>Problem

Podczas rejestrowania komputera z systemem Windows w zarządzania aktualizacjami są widoczne aktualizacje zainstalowane bez wdrożenia.

### <a name="cause"></a>Przyczyna

W systemie Windows aktualizacje są instalowane automatycznie, gdy tylko będą dostępne. To zachowanie może spowodować zamieszanie, jeśli nie zaplanowałeś aktualizacji do wdrożenia na komputerze.

### <a name="resolution"></a>Rozwiązanie

Klucz `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` rejestru domyślnie ma ustawienie 4: `auto download and install`.

W przypadku klientów usługi Update Management zalecamy `auto download but do not auto install`ustawienie tego klucza na 3: .

Aby uzyskać więcej informacji, zobacz [Konfigurowanie aktualizacji automatycznych](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scenariusz: Maszyna jest już zarejestrowana na innym koncie

### <a name="issue"></a>Problem

Zostanie wyświetlony następujący komunikat o błędzie:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Przyczyna

Komputer został już włączony do innego obszaru roboczego do zarządzania aktualizacjami.

### <a name="resolution"></a>Rozwiązanie

1. Wykonaj kroki w obszarze [Maszyny nie są wyświetlane w portalu w obszarze Zarządzanie aktualizacjami,](#nologs) aby upewnić się, że komputer zgłasza się do właściwego obszaru roboczego.
2. Oczyść artefakty na komputerze, [usuwając grupę hybrydowego śmiwalnika,](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)a następnie spróbuj ponownie.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Scenariusz: Maszyna nie może komunikować się z usługą

### <a name="issue"></a>Problem

Pojawia się jeden z następujących komunikatów o błędach:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Przyczyna

Serwer proxy, brama lub zapora mogą blokować komunikację sieciową. 

### <a name="resolution"></a>Rozwiązanie

Przejrzyj sieć i upewnij się, że odpowiednie porty i adresy są dozwolone. Zobacz [wymagania sieciowe](../automation-hybrid-runbook-worker.md#network-planning) dotyczące listy portów i adresów wymaganych przez usługi Zarządzanie aktualizacjami i hybrydowe procesy zarządzania.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Scenariusz: nie można utworzyć certyfikatu z podpisem własnym

### <a name="issue"></a>Problem

Pojawia się jeden z następujących komunikatów o błędach:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Przyczyna

Hybrydowy proces roboczy uruchomieniu nie może wygenerować certyfikatu z podpisem własnym.

### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy konto systemowe ma dostęp do odczytu do folderu **C:\ProgramData\Microsoft\Crypto\RSA** i spróbuj ponownie.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Scenariusz: Zaplanowana aktualizacja nie powiodła się z powodu błędu MaintenanceWindowExceeded

### <a name="issue"></a>Problem

Domyślne okno konserwacji aktualizacji wynosi 120 minut. Okno konserwacji można wydłużyć do maksymalnie 6 godzin lub 360 minut.

### <a name="resolution"></a>Rozwiązanie

Edytuj wszystkie nieudane zaplanowane wdrożenia aktualizacji i zwiększ okno konserwacji.

Aby uzyskać więcej informacji na temat okien konserwacji, zobacz [Instalowanie aktualizacji](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Scenariusz: Maszyna jest wyświetlana jako "Nie oceniona" i pokazuje wyjątek HRESULT

### <a name="issue"></a>Problem

* Masz maszyny, które `Not assessed` są wyświetlane jako w obszarze **Zgodność**, a pod nimi zostanie wyświetlony komunikat o wyjątku.
* W portalu jest widoczny kod błędu HRESULT.

### <a name="cause"></a>Przyczyna

Agent aktualizacji (Windows Update Agent w systemie Windows; menedżer pakietów dla dystrybucji systemu Linux) nie jest poprawnie skonfigurowany. Zarządzanie aktualizacjami opiera się na agenta aktualizacji komputera, aby zapewnić aktualizacje, które są potrzebne, stan poprawki i wyniki wdrożonych poprawek. Bez tych informacji zarządzanie aktualizacjami nie może poprawnie raportować poprawek, które są potrzebne lub zainstalowane.

### <a name="resolution"></a>Rozwiązanie

Spróbuj wykonać aktualizacje lokalnie na komputerze. Jeśli to się nie powiedzie, zazwyczaj oznacza to, że występuje błąd konfiguracji z agentem aktualizacji.

Ten problem jest często spowodowany problemami z konfiguracją sieci i zaporą. Spróbuj wykonać następujące kroki:

* W przypadku systemu Linux sprawdź odpowiednią dokumentację, aby upewnić się, że można dotrzeć do punktu końcowego sieci repozytorium pakietów.
* W systemie Windows sprawdź konfigurację agenta wymienioną w [obszarze Aktualizacje, która nie jest pobierana z punktu końcowego intranetu (WSUS/SCCM).](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)
  * Jeśli maszyny są skonfigurowane dla usługi Windows Update, upewnij się, że można dotrzeć do punktów końcowych opisanych w [problemy związane z HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Jeśli maszyny są skonfigurowane dla usług Windows Server Update Services (WSUS), upewnij się, że można dotrzeć do serwera WSUS skonfigurowanego przez [klucz rejestru WUServer](/windows/deployment/update/waas-wu-settings).

Jeśli widzisz HRESULT, kliknij dwukrotnie wyjątek wyświetlany na czerwono, aby wyświetlić cały komunikat o wyjątku. Przejrzyj następującą tabelę potencjalnych rozwiązań lub zalecanych działań:

|Wyjątek  |Rozdzielczość lub działanie  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Przeszukaj odpowiedni kod błędu na [liście kodów błędów aktualizacji systemu Windows,](https://support.microsoft.com/help/938205/windows-update-error-code-list) aby znaleźć dodatkowe informacje na temat przyczyny wyjątku.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Wskazują one na problemy z łącznością sieciową. Upewnij się, że komputer ma łączność sieciową z zarządzaniem aktualizacjami. Zobacz sekcję [planowania sieci,](../automation-update-management.md#ports) aby uzyskać listę wymaganych portów i adresów.        |
|`0x8024001E`| Operacja aktualizacji nie została ukończona, ponieważ usługa lub system został zamknięty.|
|`0x8024002E`| Usługa Windows Update jest wyłączona.|
|`0x8024402C`     | Jeśli używasz serwera WSUS, upewnij się, `WUServer` że `WUStatusServer` wartości `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` rejestru dla i pod kluczem rejestru określają poprawny serwer WSUS.        |
|`0x80072EE2`|Występuje problem z łącznością sieciową lub problem podczas rozmowy ze skonfigurowanym serwerem programu WSUS. Sprawdź ustawienia usługi WSUS i upewnij się, że usługa jest dostępna z klienta.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Upewnij się, że usługa Windows Update (wuauserv) jest uruchomiona i nie jest wyłączona.        |
|`0x80070005`| Błąd odmowy dostępu może być spowodowany przez jedną z następujących czynności:<br> Zainfekowany komputer<br> Ustawienia usługi Windows Update nie zostały poprawnie skonfigurowane<br> Błąd uprawnień do pliku w folderze %WinDir%\SoftwareDistribution<br> Za mało miejsca na dysku na dysku systemowym (C:).
|Każdy inny wyjątek rodzajowy     | Uruchom wyszukiwanie w Internecie w poszukiwaniu możliwych rozwiązań i pracuj z lokalnym wsparciem IT.         |

Przeglądanie pliku %Windir%\Windowsupdate.log może również pomóc w określeniu możliwych przyczyn. Aby uzyskać więcej informacji na temat sposobu odczytywania dziennika, zobacz [Jak odczytać plik Windowsupdate.log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Można również pobrać i uruchomić narzędzie do [rozwiązywania problemów z usługą Windows Update,](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) aby sprawdzić, czy nie występują problemy z usługą Windows Update na komputerze.

> [!NOTE]
> Dokumentacja [narzędzia do rozwiązywania problemów z usługą Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) wskazuje, że jest ona używana na klientach systemu Windows, ale działa również na systemie Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scenariusz: Aktualizacja uruchom zwraca stan "Failed" (Linux)

### <a name="issue"></a>Problem

Rozpoczyna się uruchamianie aktualizacji, ale napotyka błędy podczas uruchamiania.

### <a name="cause"></a>Przyczyna

Możliwe przyczyny:

* Menedżer pakietów jest w złej kondycji.
* Agent aktualizacji (WUA dla systemu Windows, menedżer pakietów specyficzny dla dystrybucji dla systemu Linux) jest nieprawidłowo skonfigurowany.
* Określone pakiety zakłócają poprawki oparte na chmurze.
* Urządzenie jest nieosiągalne.
* Aktualizacje miały zależności, które nie zostały rozwiązane.

### <a name="resolution"></a>Rozwiązanie

Jeśli wystąpią błędy podczas uruchamiania aktualizacji po pomyślnym uruchomieniu, [sprawdź dane wyjściowe zadania](../manage-update-multi.md#view-results-of-an-update-deployment) z komputera, którego dotyczy problem w trakcie pracy. Możesz znaleźć określone komunikaty o błędach z maszyn, które można badać i podejmować działania. Zarządzanie aktualizacjami wymaga, aby menedżer pakietów był w dobrej kondycji w celu pomyślnego wdrożenia aktualizacji.

Jeśli określone poprawki, pakiety lub aktualizacje są widoczne bezpośrednio przed niepowodzeniem zadania, można spróbować [wykluczyć](../automation-tutorial-update-management.md#schedule-an-update-deployment) te z następnego wdrożenia aktualizacji. Aby zebrać informacje dziennika z witryny Windows Update, zobacz [Pliki dziennika usługi Windows Update](/windows/deployment/update/windows-update-logs).

Jeśli nie możesz rozwiązać problemu z poprawkami, zrób kopię następującego pliku dziennika i zachowaj go do rozwiązywania problemów przed rozpoczęciem następnego wdrożenia aktualizacji:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>Poprawki nie są zainstalowane

### <a name="machines-dont-install-updates"></a>Maszyny nie instalują aktualizacji

Spróbuj uruchomić aktualizacje bezpośrednio na maszynie. Jeśli urządzenie nie może zastosować aktualizacji, zapoznaj się [z listą potencjalnych błędów w przewodniku rozwiązywania problemów](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).

Jeśli aktualizacje są uruchamiane lokalnie, spróbuj usunąć i ponownie zainstalować agenta na komputerze, postępując zgodnie ze wskazówkami dotyczącymi [usuwania maszyny wirtualnej z zarządzania aktualizacjami](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Wiem, że aktualizacje są dostępne, ale nie są wyświetlane jako dostępne na moich komputerach

Dzieje się tak często, jeśli maszyny są skonfigurowane do uruchamiania aktualizacji z programu WSUS lub programu Microsoft Endpoint Configuration Manager, ale program WSUS i program Menedżer konfiguracji nie zatwierdziła aktualizacji.

Można sprawdzić, czy maszyny są skonfigurowane dla programów WSUS i `UseWUServer` SCCM, odwołując się do klucza rejestru do kluczy rejestru w [sekcji Konfigurowanie aktualizacji automatycznych, edytując sekcję Rejestru w tym artykule](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Jeśli aktualizacje nie są zatwierdzone w umia dla systemu WSUS, nie są zainstalowane. Możesz sprawdzić, czy nie są zatwierdzone aktualizacje w usłudze Log Analytics, uruchamiając następującą kwerendę.

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Aktualizacje są wyświetlane jako zainstalowane, ale nie mogę ich znaleźć na swojej maszynie

Aktualizacje są często zastępowane przez inne aktualizacje. Aby uzyskać więcej informacji, zobacz [Aktualizacja została zastąpiona](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) w przewodniku rozwiązywania problemów z usługą Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Instalowanie aktualizacji według klasyfikacji w systemie Linux

Wdrażanie aktualizacji w systemie Linux według klasyfikacji („Aktualizacje krytyczne i zabezpieczeń”) ma ważne zastrzeżenia, szczególnie w przypadku systemu CentOS. Ograniczenia te są udokumentowane na [stronie Omówienie zarządzania aktualizacjami](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 stale brakuje

KB2267602 to [aktualizacja definicji usługi Windows Defender](https://www.microsoft.com/wdsi/definitions). Jest aktualizowana codziennie.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz [@AzureSupport](https://twitter.com/azuresupport)się z oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Złóż zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.
