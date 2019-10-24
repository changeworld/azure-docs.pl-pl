---
title: Konfigurowanie skryptów przed i po wdrożeniu Update Management na platformie Azure
description: W tym artykule opisano sposób konfigurowania i zarządzania skryptami wstępnymi i skryptami dla wdrożeń aktualizacji.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 860eaf5d37b3d3064e3b10bd1dab02c04b95ab5b
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755508"
---
# <a name="manage-pre-and-post-scripts"></a>Zarządzanie skryptami wstępnymi i po nim

Skrypty przed skryptami i skrypty po skrypcie umożliwiają uruchamianie elementów Runbook programu PowerShell na koncie Azure Automation przed (przed zadaniami) i po nim (po zadaniu) wdrożenia aktualizacji. Skrypty wstępne i po wykonaniu są uruchamiane w kontekście platformy Azure, a nie lokalnie. Przed rozpoczęciem wdrażania aktualizacji skrypty przeduruchomieniowe są uruchamiane na początku. Po wykonaniu tych czynności skrypty po zakończeniu wdrożenia i po każdym skonfigurowanym ponownym uruchomieniu zostaną uruchomione.

## <a name="runbook-requirements"></a>Wymagania elementu Runbook

Aby element Runbook był używany jako skrypt pre lub po stronie klienta, należy zaimportować element Runbook do konta usługi Automation i opublikować go. Aby dowiedzieć się więcej o tym procesie, zobacz temat [Publikowanie elementu Runbook](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-pre-script-or-post-script"></a>Używanie skryptu wstępnego lub skryptu po skrypcie

Aby użyć skryptu wstępnego lub po skrypcie w wdrożeniu aktualizacji, Zacznij od utworzenia wdrożenia aktualizacji. Wybierz polecenie **pre-scripts + post-scripts**. Ta akcja powoduje otwarcie strony **Wybieranie skryptów przed skryptami + po skrypcie** .

![Wybieranie skryptów](./media/pre-post-scripts/select-scripts.png)

Wybierz skrypt, którego chcesz użyć. W tym przykładzie używamy elementu Runbook **UpdateManagement-TurnOnVms** . Po wybraniu elementu Runbook zostanie otwarta strona **Konfigurowanie skryptu** . Wybierz opcję **pre-Script**, a następnie wybierz przycisk **OK**.

Powtórz ten proces dla skryptu **UpdateManagement-TurnOffVms** . Ale w przypadku wybrania **typu skryptu**wybierz pozycję **po skrypcie**.

Sekcja **wybrane elementy** zawiera teraz wszystkie wybrane skrypty. Jednym z nich jest skrypt poprzedzający, a drugi to skrypt po stronie:

![Wybrane elementy](./media/pre-post-scripts/selected-items.png)

Zakończ konfigurowanie wdrożenia aktualizacji.

Po zakończeniu wdrażania aktualizacji możesz przejść do obszaru **wdrożenia aktualizacji** , aby wyświetlić wyniki. Jak widać, stan jest dostarczany dla skryptu wstępnego i po skrypcie:

![Aktualizowanie wyników](./media/pre-post-scripts/update-results.png)

Po wybraniu uruchomienia wdrożenia aktualizacji są wyświetlane dodatkowe szczegóły dotyczące skryptów pre i post. Podano link do źródła skryptu w czasie wykonywania.

![Wyniki uruchamiania wdrożenia](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Przekazywanie parametrów

Konfigurując skrypty wstępne i po wykonaniu skryptów, można przekazać parametry, tak jak planowanie elementu Runbook. Parametry są definiowane w momencie tworzenia wdrożenia aktualizacji. Wstępne i po skrypcie obsługują następujące typy:

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

Oprócz standardowych parametrów elementu Runbook podano inny parametr: **SoftwareUpdateConfigurationRunContext**

Ten parametr jest ciągiem JSON, a jeśli parametr zostanie zdefiniowany we wstępnie lub po skrypcie, jest on automatycznie przekazywać przez wdrożenie aktualizacji. Parametr zawiera informacje o wdrożeniu aktualizacji, który jest podzbiorem informacji zwracanych przez [interfejs API SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). 

W poniższej tabeli przedstawiono właściwości, które są podane w zmiennej.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Właściwości SoftwareUpdateConfigurationRunContext

|Właściwość  |Opis  |
|---------|---------|
|. Softwareupdateconfigurationname     | Nazwa konfiguracji aktualizacji oprogramowania.        |
|. Softwareupdateconfigurationrunid     | Unikatowy identyfikator dla przebiegu.        |
|SoftwareUpdateConfigurationSettings     | Kolekcja właściwości związanych z konfiguracją aktualizacji oprogramowania.         |
|SoftwareUpdateConfigurationSettings. operatingSystem     | Systemy operacyjne przeznaczone do wdrożenia aktualizacji.         |
|SoftwareUpdateConfigurationSettings. Duration     | Maksymalny czas trwania wdrożenia aktualizacji jest uruchamiany `PT[n]H[n]M[n]S` jak na ISO8601; nazywana również *oknem obsługi*.          |
|SoftwareUpdateConfigurationSettings. Windows     | Kolekcja właściwości związanych z komputerami z systemem Windows.         |
|SoftwareUpdateConfigurationSettings. Windows. excludedKbNumbers     | Lista artykułów bazy wiedzy, które są wykluczone ze wdrożenia aktualizacji.        |
|SoftwareUpdateConfigurationSettings. Windows. includedUpdateClassifications     | Klasyfikacje aktualizacji wybrane dla wdrożenia aktualizacji.        |
|SoftwareUpdateConfigurationSettings. Windows. rebootSetting     | Ustawienia ponownego uruchamiania wdrożenia aktualizacji.        |
|azureVirtualMachines     | Lista elementów resourceId dla maszyn wirtualnych platformy Azure we wdrożeniu aktualizacji.        |
|nonAzureComputerNames|Lista nazw FQDN komputerów nienależących do platformy Azure we wdrożeniu aktualizacji.|

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

Pełny przykład ze wszystkimi właściwościami można znaleźć w: [Pobieranie konfiguracji aktualizacji oprogramowania według nazwy](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> Obiekt `SoftwareUpdateConfigurationRunContext` może zawierać zduplikowane wpisy dla maszyn. Może to spowodować, że skrypty wstępne i pozostanie uruchomione na tym samym komputerze. Aby obejść ten problem, użyj `Sort-Object -Unique`, aby zaznaczyć tylko unikatowe nazwy maszyn wirtualnych w skrypcie.


## <a name="stopping-a-deployment"></a>Zatrzymywanie wdrożenia

Jeśli chcesz zatrzymać wdrożenie na podstawie skryptu wstępnego, musisz [zgłosić](automation-runbook-execution.md#throw) wyjątek. Jeśli tego nie zrobisz, wdrożenie i skrypt po skrypcie będą nadal działać. Poniższy fragment kodu pokazuje, jak zgłosić wyjątek.

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

Przykłady dla skryptów pre i po nim można znaleźć w [galerii centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) i w [Galeria programu PowerShell](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)lub zaimportować je za pomocą Azure Portal. Aby to zrobić, na koncie usługi Automation w obszarze **Automatyzacja procesów**wybierz pozycję **Galeria elementów Runbook**. Użyj **Update Management** filtru.

![Lista galerii](./media/pre-post-scripts/runbook-gallery.png)

Możesz też wyszukać je według nazwy skryptu, jak pokazano na poniższej liście:

* Update Management Włącz maszyny wirtualne
* Update Management — Wyłącz maszyny wirtualne
* Update Management — uruchom skrypt lokalnie
* Szablon Update Management na potrzeby skryptów poprzedzających/post
* Update Management — uruchom skrypt z poleceniem Run

> [!IMPORTANT]
> Po zaimportowaniu elementów Runbook należy je opublikować, aby można było ich użyć. Aby to zrobić, Znajdź element Runbook na koncie usługi Automation, wybierz pozycję **Edytuj**, a następnie wybierz pozycję **Publikuj**.

Wszystkie te przykłady są zależne od szablonu podstawowego zdefiniowanego w poniższym przykładzie. Ten szablon może służyć do tworzenia własnego elementu Runbook do użycia z pre i po skrypcie. Dołączono logikę niezbędną do uwierzytelniania za pomocą platformy Azure i obsługi parametru `SoftwareUpdateConfigurationRunContext`.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
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

Zadania przed i po stronie są uruchamiane jako elementy Runbook na koncie usługi Automation, a nie bezpośrednio na maszynach we wdrożeniu. Zadania przed i po wykonaniu są również uruchamiane w kontekście platformy Azure i nie mają dostępu do maszyn spoza platformy Azure. W poniższych sekcjach pokazano, jak można bezpośrednio korzystać z maszyn, niezależnie od tego, czy są to maszyny wirtualne platformy Azure, czy na maszynach spoza platformy Azure.

### <a name="interacting-with-azure-machines"></a>Korzystanie z maszyn platformy Azure

Zadania wstępne i po wykonaniu są uruchamiane jako elementy Runbook i nie są natywnie uruchamiane na maszynach wirtualnych platformy Azure we wdrożeniu. Aby móc korzystać z maszyn wirtualnych platformy Azure, musisz dysponować następującymi elementami:

* Konto Uruchom jako
* Element Runbook, który chcesz uruchomić

Aby móc korzystać z maszyn platformy Azure, użyj polecenia cmdlet [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) , aby móc korzystać z maszyn wirtualnych platformy Azure. Aby zapoznać się z przykładem, jak to zrobić, zobacz przykład elementu Runbook [Update Management — uruchom skrypt z poleceniem Run](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Korzystanie z maszyn nienależących do platformy Azure

Zadania przed i po wykonaniu są wykonywane w kontekście platformy Azure i nie mają dostępu do maszyn spoza platformy Azure. Aby móc korzystać z maszyn spoza platformy Azure, musisz dysponować następującymi elementami:

* Konto Uruchom jako
* Na maszynie zainstalowano hybrydowy proces roboczy elementu Runbook
* Element Runbook, który ma być uruchamiany lokalnie
* Nadrzędny element Runbook

Aby można było korzystać z maszyn nienależących do platformy Azure, nadrzędny element Runbook jest uruchamiany w kontekście platformy Azure. Ten element Runbook wywołuje podrzędny element Runbook za pomocą polecenia cmdlet [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) . Należy określić parametr `-RunOn` i podać nazwę hybrydowego procesu roboczego elementu Runbook, na którym ma być uruchamiany skrypt. Aby uzyskać więcej informacji, zobacz przykład elementu Runbook [Update Management — uruchom skrypt lokalnie](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Przerwij wdrożenie poprawki

Jeśli skrypt poprzedzający błąd, może być konieczne przerwanie wdrożenia. W tym celu należy [zgłosić](/powershell/module/microsoft.powershell.core/about/about_throw) błąd w skrypcie dla każdej logiki, która spowodowałaby wystąpienie błędu.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>Znane problemy

* Nie można przekazać wartości logicznych, obiektów ani tablic do parametrów, gdy używasz funkcji pre i po skrypcie. Jeśli to zrobisz, element Runbook zakończy się niepowodzeniem. Aby uzyskać pełną listę obsługiwanych typów, zobacz [przekazywanie parametrów](#passing-parameters).

## <a name="next-steps"></a>Następne kroki

Przejdź do następującego samouczka, aby dowiedzieć się, jak zarządzać aktualizacjami dla maszyn wirtualnych z systemem Windows:

> [!div class="nextstepaction"]
> [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows Azure](automation-tutorial-update-management.md)

