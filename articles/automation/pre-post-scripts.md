---
title: Konfigurowanie skryptów pre i post dla wdrożenia Update Management na platformie Azure
description: W tym artykule opisano sposób konfigurowania skryptów pre i post na potrzeby wdrożeń aktualizacji oraz zarządzania nimi.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f13851dd43c80a63ec628e04b98271894c15afc0
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69542859"
---
# <a name="manage-pre-and-post-scripts"></a>Zarządzanie skryptami wstępnymi i gotowymi

Skrypty pre i post umożliwiają uruchamianie elementów Runbook programu PowerShell na koncie usługi Automation przed (przed zadaniami) i po nim (po zadaniu) wdrożenia aktualizacji. Skrypty wstępne i końcowe są uruchamiane w kontekście platformy Azure, a nie lokalnie. Przed rozpoczęciem wdrażania aktualizacji skrypty przeduruchomieniowe są uruchamiane na początku. Po zakończeniu wdrażania i po każdym skonfigurowanym ponownym uruchomieniu skryptów należy je uruchomić.

## <a name="runbook-requirements"></a>Wymagania elementu Runbook

Aby element Runbook był używany jako skrypt gotowy lub po stronie klienta, należy zaimportować element Runbook do konta usługi Automation i opublikować go. Aby dowiedzieć się więcej o tym procesie, zobacz [Publikowanie elementu Runbook](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-prepost-script"></a>Używanie skryptu wstępnego/po

Aby użyć skryptu wstępnego i lub po zainicjowaniu aktualizacji, Zacznij od utworzenia wdrożenia aktualizacji. Wybierz polecenie **pre-scripts + post scripts**. Ta akcja powoduje otwarcie strony **Wybieranie skryptów przed skryptami + po skrypcie** .  

![Wybieranie skryptów](./media/pre-post-scripts/select-scripts.png)

Wybierz skrypt, którego chcesz użyć, w tym przykładzie użyto elementu Runbook **UpdateManagement-TurnOnVms** . Po wybraniu elementu Runbook zostanie otwarta strona **Konfigurowanie skryptu** wybierz polecenie **pre-Script**. Po zakończeniu kliknij przycisk **OK** .

Powtórz ten proces dla skryptu **UpdateManagement-TurnOffVms** . Ale podczas wybierania **typu skryptu**wybierz polecenie **po skrypcie**.

W sekcji **wybrane elementy** są teraz wyświetlane skrypty, które zostały wybrane i włączone, są wstępnie skryptem, a drugi to skrypt po skrypcie.

![Wybrane elementy](./media/pre-post-scripts/selected-items.png)

Zakończ konfigurowanie wdrożenia aktualizacji.

Po zakończeniu wdrażania aktualizacji możesz przejść do obszaru **wdrożenia aktualizacji** , aby wyświetlić wyniki. Jak widać, udostępniany jest stan poprzedzający skrypt i skrypt po skrypcie.

![Aktualizowanie wyników](./media/pre-post-scripts/update-results.png)

Klikając w przebiegu wdrożenia aktualizacji, są podane dodatkowe szczegóły dotyczące skryptów pre i post. Podano link do źródła skryptu w czasie wykonywania.

![Wyniki uruchamiania wdrożenia](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Przekazywanie parametrów

Podczas konfigurowania skryptów pre i post można przekazać parametry w taki sam sposób, jak planowanie elementu Runbook. Parametry są definiowane w momencie tworzenia wdrożenia aktualizacji. Skrypty wstępne i końcowe obsługują następujące typy:

* delikatn
* Bajc
* ZAOKR
* długo
* dokładności
* wiersz
* Double
* Datę
* parametry

Jeśli potrzebujesz innego typu obiektu, możesz rzutować go na inny typ z własną logiką w elemencie Runbook.

Oprócz standardowych parametrów elementu Runbook podano dodatkowy parametr. Ten parametr to **SoftwareUpdateConfigurationRunContext**. Ten parametr jest ciągiem JSON, a jeśli parametr zostanie zdefiniowany w skrypcie pre lub post, jest on automatycznie przekazywać przez wdrożenie aktualizacji. Parametr zawiera informacje o wdrożeniu aktualizacji, który jest podzbiorem informacji zwracanych przez [interfejs API SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) w poniższej tabeli przedstawiono właściwości, które są dostępne w zmiennej:


### <a name="softwareupdateconfigurationruncontext-properties"></a>Właściwości SoftwareUpdateConfigurationRunContext

|Właściwość  |Opis  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Nazwa konfiguracji aktualizacji oprogramowania        |
|SoftwareUpdateConfigurationRunId     | Unikatowy identyfikator dla przebiegu.        |
|SoftwareUpdateConfigurationSettings     | Kolekcja właściwości związanych z konfiguracją aktualizacji oprogramowania         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Systemy operacyjne przeznaczone do wdrożenia aktualizacji         |
|SoftwareUpdateConfigurationSettings.duration     | Maksymalny czas trwania wdrożenia aktualizacji działa tak jak `PT[n]H[n]M[n]S` na ISO8601, nazywane również "oknem obsługi"          |
|SoftwareUpdateConfigurationSettings.Windows     | Kolekcja właściwości związanych z komputerami z systemem Windows         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Lista artykułów bazy wiedzy wykluczonych ze wdrożenia aktualizacji        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Klasyfikacje aktualizacji wybrane dla wdrożenia aktualizacji        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Ustawienia ponownego uruchamiania wdrożenia aktualizacji        |
|azureVirtualMachines     | Lista elementów resourceId dla maszyn wirtualnych platformy Azure w ramach wdrożenia aktualizacji        |
|nonAzureComputerNames|Lista nazw FQDN komputerów nienależących do platformy Azure we wdrożeniu aktualizacji|

Poniższy przykład jest ciągiem JSON przekazaną do parametru **SoftwareUpdateConfigurationRunContext** :

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

Pełny przykład ze wszystkimi właściwościami można znaleźć pod adresem: [Konfiguracje aktualizacji oprogramowania — pobieranie według nazwy](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> `SoftwareUpdateConfigurationRunContext` Obiekt może zawierać zduplikowane wpisy dla maszyn. Może to spowodować, że skrypty wstępne i końcowe będą uruchamiane wiele razy na tym samym komputerze. Aby obejść to zachowanie, `Sort-Object -Unique` Użyj, aby wybrać tylko unikatowe nazwy maszyn wirtualnych w skrypcie.


## <a name="stopping-a-deployment"></a>Zatrzymywanie wdrożenia

Jeśli chcesz zatrzymać wdrożenie na podstawie skryptu wstępnego, musisz [zgłosić](automation-runbook-execution.md#throw) wyjątek. Jeśli wyjątek nie zostanie zgłoszony, wdrożenie i skrypt po skrypcie będą nadal działać. [Przykładowy element Runbook](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44?redir=0) w galerii pokazuje, jak to zrobić. Poniżej znajduje się fragment kodu z tego elementu Runbook.

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


## <a name="samples"></a>Przykłady

Przykłady dla skryptów pre i post można znaleźć w [galerii centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell)lub zaimportować za pomocą Azure Portal. Aby zaimportować je za pomocą portalu, na koncie usługi Automation w obszarze **Automatyzacja procesów**wybierz pozycję **Galeria elementów Runbook**. Użyj **Update Management** filtru.

![Lista galerii](./media/pre-post-scripts/runbook-gallery.png)

Możesz też wyszukać je według nazwy skryptu, jak pokazano na poniższej liście:

* Update Management Włącz maszyny wirtualne
* Update Management — Wyłącz maszyny wirtualne
* Update Management — uruchom skrypt lokalnie
* Szablon Update Management na potrzeby skryptów poprzedzających/post
* Update Management — uruchom skrypt z poleceniem Run

> [!IMPORTANT]
> Po zaimportowaniu elementów Runbook należy je **opublikować** , aby można było ich użyć. Aby to zrobić, Znajdź element Runbook na koncie usługi Automation, wybierz pozycję **Edytuj**, a następnie kliknij pozycję **Publikuj**.

Wszystkie te przykłady są zależne od szablonu podstawowego zdefiniowanego w poniższym przykładzie. Ten szablon może służyć do tworzenia własnego elementu Runbook do użycia ze skryptami wstępnymi i gotowymi. Dołączono logikę niezbędną do uwierzytelniania za pomocą `SoftwareUpdateConfigurationRunContext` platformy Azure i obsługi parametru.

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

## <a name="interacting-with-machines"></a>Korzystanie z maszyn

Zadania wstępne i końcowe są uruchamiane jako elementy Runbook na koncie usługi Automation, a nie bezpośrednio na maszynach we wdrożeniu. Zadania przed i po wykonaniu są również uruchamiane w kontekście platformy Azure i nie mają dostępu do maszyn spoza platformy Azure. W poniższych sekcjach pokazano, jak można korzystać z maszyn bezpośrednio, niezależnie od tego, czy są one maszyną wirtualną platformy Azure, czy maszyną spoza platformy Azure:

### <a name="interacting-with-azure-machines"></a>Korzystanie z maszyn platformy Azure

Zadania przed i po wykonaniu są uruchamiane jako elementy Runbook i nie są natywnie uruchamiane na maszynach wirtualnych platformy Azure we wdrożeniu. Aby móc korzystać z maszyn wirtualnych platformy Azure, musisz dysponować następującymi elementami:

* Konto Uruchom jako
* Element Runbook, który chcesz uruchomić

Aby móc korzystać z maszyn platformy Azure, użyj polecenia cmdlet [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) , aby móc korzystać z maszyn wirtualnych platformy Azure. Aby zapoznać się z przykładem, jak to zrobić, zobacz przykład elementu Runbook [Update Management — uruchom skrypt z poleceniem Run](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Korzystanie z maszyn nienależących do platformy Azure

Zadania przed i po wykonaniu są uruchamiane w kontekście platformy Azure i nie mają dostępu do maszyn spoza platformy Azure. Aby móc korzystać z maszyn spoza platformy Azure, musisz dysponować następującymi elementami:

* Konto Uruchom jako
* Na maszynie zainstalowano hybrydowy proces roboczy elementu Runbook
* Element Runbook, który ma być uruchamiany lokalnie
* Nadrzędny element Runbook

Aby można było korzystać z maszyn nienależących do platformy Azure, nadrzędny element Runbook jest uruchamiany w kontekście platformy Azure. Ten element Runbook wywołuje podrzędny element Runbook za pomocą polecenia cmdlet [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) . Należy określić `-RunOn` parametr i podać nazwę hybrydowego procesu roboczego elementu Runbook, na którym ma być uruchamiany skrypt. Aby zapoznać się z przykładem, jak to zrobić, zobacz przykład elementu Runbook [Update Management — uruchom skrypt lokalnie](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Przerwij wdrożenie poprawki

Jeśli skrypt przedniego zwróci błąd, może być konieczne przerwanie wdrożenia. W tym celu należy [zgłosić](/powershell/module/microsoft.powershell.core/about/about_throw) błąd w skrypcie dla każdej logiki, która spowodowałaby wystąpienie błędu.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.  
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>Znane problemy

* Nie można przekazać wartości logicznych, obiektów lub tablic do parametrów podczas korzystania ze skryptów pre i post. Element Runbook zakończy się niepowodzeniem. Aby uzyskać pełną listę obsługiwanych typów, zobacz [Parametry](#passing-parameters).

## <a name="next-steps"></a>Następne kroki

Przejdź do samouczka, aby dowiedzieć się, jak zarządzać aktualizacjami dla maszyn wirtualnych z systemem Windows.

> [!div class="nextstepaction"]
> [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows Azure](automation-tutorial-update-management.md)

