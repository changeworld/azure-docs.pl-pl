---
title: Konfigurowanie skryptów przed i po wdrożeniu rozwiązania Update Management na platformie Azure (wersja zapoznawcza)
description: W tym artykule opisano sposób konfigurowania i zarządzania przed i po skryptów dla wdrożenia aktualizacji
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 76cd877380090ccad8b2f7b7dbe79957e0eab5bb
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263812"
---
# <a name="manage-pre-and-post-scripts-preview"></a>Zarządzanie skryptami przed i po (wersja zapoznawcza)

Skrypty przed i po umożliwiają uruchamianie elementów runbook programu PowerShell na koncie usługi Automation, przed (zadań wstępne) i po wdrożeniu (po zadanie) aktualizacji. Skrypty przed i po Uruchom w kontekście platformy Azure, a nie lokalnie. Wstępne skrypty uruchamiane po rozpoczęciu wdrażania aktualizacji. Opublikuj skrypty uruchamiane po zakończeniu wdrożenia i za wszelkie ponowne uruchomienia, które są skonfigurowane.

## <a name="runbook-requirements"></a>Wymagania elementów Runbook

Element runbook ma być używany jako skrypt przed lub po elementu runbook musi zostać zaimportowane do konta usługi automation i opublikowane. Aby dowiedzieć się więcej o tym procesie, zobacz [publikowanie elementu runbook](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-prepost-script"></a>Za pomocą skryptu pre lub używanego po nim

Aby użyć pre i lub post skryptu we wdrożeniu aktualizacji, Rozpocznij od utworzenia wdrożenia aktualizacji. Wybierz **skryptu poprzedzającego + skrypty Post (wersja zapoznawcza)**. Ta akcja powoduje otwarcie **wybierz skryptu poprzedzającego i skryptu używanego po utworzeniu** strony.  

![Wybierz skryptów](./media/pre-post-scripts/select-scripts.png)

Wybierz odpowiedni skrypt, które chcesz użyć w tym przykładzie, możesz użyć **UpdateManagement TurnOnVms** elementu runbook. Po wybraniu elementu runbook **skonfigurować skrypt** zostanie otwarta strona, podaj wartości parametrów, a następnie wybierz **Pre-Script**. Kliknij przycisk **OK** po zakończeniu.

![Skryptu konfiguracji](./media/pre-post-scripts/configure-script.png)

Powtórz ten proces dla **UpdateManagement TurnOffVms** skryptu. Jednak przy wyborze **typ skryptu**, wybierz **skryptu używanego po utworzeniu**.

**Wybrane elementy** sekcja zawiera obecnie zarówno ze skryptami zaznaczone i jest skryptu poprzedzającego, a drugi to skrypt używany po utworzeniu.

![Wybrane elementy](./media/pre-post-scripts/selected-items.png)

Zakończ konfigurowanie wdrożenia aktualizacji.

Po zakończeniu wdrożenia aktualizacji, możesz przejść do **wdrożenia aktualizacji** , aby wyświetlić wyniki. Jak widać, znajdują się stan skryptu poprzedzającego i skryptu używanego po utworzeniu.

![Aktualizowanie wyników](./media/pre-post-scripts/update-results.png)

Klikając do wdrożenia aktualizacji, uruchom teraz podać dodatkowe szczegóły, aby skrypty przed i po. Znajduje się link do źródła skryptu w czasie uruchomienia.

![Wyniki przebiegu wdrożenia](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Przekazywanie parametrów

Po skonfigurowaniu pre i post skryptów, możesz przekazać parametry, podobnie jak planowanie elementu runbook. Parametry są definiowane w czasie tworzenia wdrożenia aktualizacji. Skrypty przed i po obsługuje następujących typów:

* [char]
* [bajt]
* [int]
* [długie]
* [dziesiętna]
* [pojedynczego]
* [double]
* [Data i godzina]
* [string]

Jeśli potrzebujesz innego typu obiektu, można go rzutować na inny typ, z własną logiką w elemencie runbook.

Oprócz parametry elementu runbook standardowe dodatkowy parametr jest dostępna. Ten parametr jest **SoftwareUpdateConfigurationRunContext**. Ten parametr jest ciąg JSON, a Jeśli zdefiniujesz parametru w skrypcie pre lub wpis, automatycznie jest przekazywana w przez wdrożenie aktualizacji. Parametr zawiera informacje dotyczące wdrażania aktualizacji, która jest podzbiorem informacje zwrócone przez [SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) poniższej tabeli przedstawiono właściwości, które znajdują się w zmiennej:

## <a name="stopping-a-deployment"></a>Zatrzymywanie wdrożenia

Jeśli chcesz zatrzymać wdrożenie na podstawie wstępnie skryptu należy [throw](automation-runbook-execution.md#throw) wyjątek. Jeśli użytkownik nie zgłasza wyjątku, wdrażania i skrypt Post będą nadal działać. [Przykładowy element runbook](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44?redir=0) w galerii pokazuje, jak to zrobić. Poniżej przedstawiono fragment z tego elementu runbook.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.  
        throw $summary.Summary
    }
}
```

### <a name="softwareupdateconfigurationruncontext-properties"></a>Właściwości SoftwareUpdateConfigurationRunContext

|Właściwość  |Opis  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Nazwa konfiguracji aktualizacji oprogramowania        |
|SoftwareUpdateConfigurationRunId     | Unikatowy identyfikator uruchomienia.        |
|SoftwareUpdateConfigurationSettings     | Zbiór właściwości związane z konfiguracji aktualizacji oprogramowania         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Systemy operacyjne przeznaczone dla wdrożenia aktualizacji         |
|SoftwareUpdateConfigurationSettings.duration     | Maksymalny czas trwania wdrożenia aktualizacji Uruchom jako `PT[n]H[n]M[n]S` zgodnie z ISO8601, nazywany również "okno konserwacji"          |
|SoftwareUpdateConfigurationSettings.Windows     | Zbiór właściwości powiązanych z komputerów Windows         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Listę artykułów bazy wiedzy, które są wykluczone z wdrożenia aktualizacji        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Aktualizuj klasyfikacje wybrane do wdrożenia aktualizacji        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Uruchom ponownie ustawienia wdrożenia aktualizacji        |
|azureVirtualMachines     | Lista identyfikatory zasobów dla maszyn wirtualnych platformy Azure objętych wdrażaniem aktualizacji        |
|nonAzureComputerNames|Lista komputerów spoza platformy Azure nazw FQDN objętych wdrażaniem aktualizacji|

Poniższy przykład jest przekazany do ciągu JSON **SoftwareUpdateConfigurationRunContext** parametru:

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ], 
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

Pełny przykład ze wszystkich właściwości można znaleźć w folderze: [Konfiguracje aktualizację oprogramowania - Get według nazwy](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> `SoftwareUpdateConfigurationRunContext` Obiekt może zawierać zduplikowanych wpisów dla maszyny. Może to spowodować, że skrypty przed i po wielokrotne uruchomienie na tym samym komputerze. Aby obejść to zachowanie, użyj `Sort-Object -Unique` wybrać tylko unikatowe nazwy maszyny Wirtualnej w skrypcie.

## <a name="samples"></a>Przykłady

Przykłady dla skryptów przed i po można znaleźć w [Galeria Centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell), lub zaimportowany w witrynie Azure portal. Aby zaimportować za pośrednictwem portalu, w ramach konta usługi Automation, w obszarze **automatyzacji procesów**, wybierz opcję **Galeria elementów Runbook**. Użyj **rozwiązania Update Management** filtru.

![Listy galerii.](./media/pre-post-scripts/runbook-gallery.png)

Lub możesz wyszukać je według nazwy skryptu jak pokazano na poniższej liście:

* Zarządzanie aktualizacjami — Włączanie na maszynach wirtualnych
* Zarządzanie aktualizacjami — wyłączanie maszyn wirtualnych
* Zarządzanie aktualizacjami — Uruchom skrypt lokalnie
* Update Management — szablon dla skryptów Pre lub używanego po nim
* Zarządzanie aktualizacjami — uruchamianie skryptu za pomocą polecenia uruchamiania

> [!IMPORTANT]
> Po zaimportowaniu elementów runbook, należy najpierw **Publikuj** je, zanim będzie można ich użyć. Czy, znaleźć elementu runbook na Twoim koncie usługi Automation wybierz **Edytuj**i kliknij przycisk **Publikuj**.

Przykłady są oparte na podstawowy szablon, który jest zdefiniowany w poniższym przykładzie. Ten szablon może służyć do tworzenia własnego elementu runbook za pomocą skryptów przed i po. Potrzebną logikę do uwierzytelniania za pomocą platformy Azure i obsługa `SoftwareUpdateConfigurationRunContext` dołączono parametr.

```powershell
<# 
.SYNOPSIS 
 Barebones script for Update Management Pre/Post 
 
.DESCRIPTION 
  This script is intended to be run as a part of Update Management Pre/Post scripts.  
  It requires a RunAs account. 
 
.PARAMETER SoftwareUpdateConfigurationRunContext 
  This is a system variable which is automatically passed in by Update Management during a deployment. 
#> 
 
param( 
    [string]$SoftwareUpdateConfigurationRunContext 
) 
#region BoilerplateAuthentication 
#This requires a RunAs account 
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection' 
 
Add-AzureRmAccount ` 
    -ServicePrincipal ` 
    -TenantId $ServicePrincipalConnection.TenantId ` 
    -ApplicationId $ServicePrincipalConnection.ApplicationId ` 
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint 
 
$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID 
#endregion BoilerplateAuthentication 
 
#If you wish to use the run context, it must be converted from JSON 
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext 
#Access the properties of the SoftwareUpdateConfigurationRunContext 
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId 
 
Write-Output $context 
 
#Example: How to create and write to a variable using the pre-script: 
<# 
#Create variable named after this run so it can be retrieved 
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false 
#Set value of variable  
Set-AutomationVariable –Name $runId -Value $vmIds 
#> 
 
#Example: How to retrieve information from a variable set during the pre-script 
<# 
$variable = Get-AutomationVariable -Name $runId 
#>      
```

## <a name="interacting-with-non-azure-machines"></a>Interakcja z maszyn spoza platformy Azure

Przed i po zadania są wykonywane w kontekście platformy Azure i nie mają dostępu do maszyny spoza platformy Azure. Do interakcji z maszyn spoza platformy Azure, należy dysponować następującymi elementami:

* Konto Uruchom jako
* Hybrydowego procesu roboczego Runbook zainstalowane na komputerze
* Element runbook, który ma być uruchomiony lokalnie
* Nadrzędny element runbook

Do interakcji z maszyn spoza platformy Azure, nadrzędny element runbook jest uruchamiane w kontekście platformy Azure. Ten element runbook wywołuje podrzędnego elementu runbook za pomocą [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) polecenia cmdlet. Należy określić `-RunOn` parametru i podaj nazwę hybrydowego procesu roboczego Runbook skrypt do uruchomienia na.

```powershell
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$resourceGroup = "AzureAutomationResourceGroup"
$aaName = "AzureAutomationAccountName"

$output = Start-AzureRmAutomationRunbook -Name "StartService" -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName -RunOn "hybridWorker"

$status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
while ($status.status -ne "Completed")
{ 
    Start-Sleep -Seconds 5
    $status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
}

$summary = Get-AzureRmAutomationJobOutput -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName

if ($summary.Type -eq "Error")
{
    Write-Error -Message $summary.Summary
}
```

## <a name="abort-patch-deployment"></a>Przerwij wdrażania poprawek

Jeśli skrypt pre zwraca błąd, można przerwać wdrożenia. Aby to zrobić, należy najpierw [throw](/powershell/module/microsoft.powershell.core/about/about_throw) błąd w skrypcie dla wszelka logika, która będzie stanowić awarii.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.  
    throw "There was an error, abort deployment"
}
```
## <a name="known-issues"></a>Znane problemy

* Nie można przekazać obiekty i tablice parametrów przy użyciu skryptów przed i po. Element runbook zakończy się niepowodzeniem.

## <a name="next-steps"></a>Kolejne kroki

Przejdź do samouczka na temat sposobu zarządzania aktualizacjami dla maszyn wirtualnych Windows.

> [!div class="nextstepaction"]
> [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows na platformie Azure](automation-tutorial-update-management.md)

