---
title: Konfigurowanie skryptów wstępnych i posuwowych we wdrażaniu usługi Update Management na platformie Azure
description: W tym artykule opisano sposób konfigurowania skryptów wstępnych i skryptów po zaktualizowaniu oraz zarządzania nimi.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 35fba966fcdb6d1c5cd7c531bb22c9c78ae16ff3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417799"
---
# <a name="manage-pre-and-post-scripts"></a>Zarządzanie skryptami wstępnymi i post-script

Skrypty wstępne i skrypty po umożliwiają uruchamianie likście programów PowerShell na koncie usługi Azure Automation przed wdrożeniem aktualizacji (przed zadaniem) i po (po zadaniu). Skrypty wstępne i post-skrypty są uruchamiane w kontekście platformy Azure, a nie lokalnie. Skrypty wstępne są uruchamiane na początku wdrażania aktualizacji. Skrypty post są uruchamiane po zakończeniu wdrażania i po każdym ponownym uruchomieniu, które są skonfigurowane.

## <a name="runbook-requirements"></a>Wymagania dotyczące ekscesu

Aby system runbook był używany jako przed lub po skrypcie, system runbook musi zostać zaimportowany do konta automatyzacji i opublikowany. Aby dowiedzieć się więcej o tym procesie, zobacz [Publikowanie likemi](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-pre-script-or-post-script"></a>Korzystanie ze skryptu wstępnego lub skryptu post-script

Aby użyć skryptu wstępnego lub skryptu po wdrożeniu aktualizacji, należy rozpocząć od utworzenia wdrożenia aktualizacji. Wybierz **skrypty wstępne + post-skrypty**. Ta akcja powoduje otwarcie strony **Wybierz skrypty wstępne + Post-skrypty.**

![Wybieranie skryptów](./media/pre-post-scripts/select-scripts.png)

Wybierz skrypt, którego chcesz użyć. W tym przykładzie używamy **updateManagement-TurnOnVms** runbook. Po wybraniu kreślić zostanie otwarta strona **Konfiguruj skrypt.** Wybierz **opcję Przedserwowy**, a następnie wybierz przycisk **OK**.

Powtórz ten proces dla skryptu **UpdateManagement-TurnOffVms.** Ale po wybraniu **typu skryptu**wybierz **opcję Post-Script**.

Sekcja **Wybrane elementy** pokazuje teraz oba wybrane skrypty. Jeden jest pre-script, a drugi jest post-script:

![Wybrane elementy](./media/pre-post-scripts/selected-items.png)

Zakończ konfigurowanie wdrożenia aktualizacji.

Po zakończeniu wdrażania aktualizacji można przejść do **aktualizacji wdrożeń,** aby wyświetlić wyniki. Jak widać, stan jest przewidziany dla pre-script i post-script:

![Aktualizuj wyniki](./media/pre-post-scripts/update-results.png)

Wybierając uruchomienie wdrożenia aktualizacji, zostaną wyświetlone dodatkowe szczegóły skryptów przed i po. Łącze do źródła skryptu w czasie uruchamiania jest dostarczane.

![Wyniki uruchomienia wdrożenia](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Przekazywanie parametrów

Podczas konfigurowania skryptów wstępnych i poskładają parametry, takie jak planowanie likścień. Parametry są definiowane w momencie tworzenia wdrożenia aktualizacji. Skrypty wstępne i post-script obsługują następujące typy:

* [char]
* [bajt]
* [int]
* [długo]
* [dziesiętne]
* [syt.]
* [double]
* [DateTime]
* [ciąg]

Jeśli potrzebujesz innego typu obiektu, można rzutować go do innego typu z własnej logiki w obiekcie runbook.

Oprócz standardowych parametrów generyczny dostępny jest inny parametr: **SoftwareUpdateConfigurationRunContext**

Ten parametr jest ciągiem JSON, a jeśli zdefiniujesz parametr w przed lub po skrypcie, jest automatycznie przekazywany przez wdrożenie aktualizacji. Parametr zawiera informacje o wdrożeniu aktualizacji, które jest podzbiorem informacji zwracanych przez [interfejs API konfiguracji SoftwareUpdate.](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) 

W poniższej tabeli przedstawiono właściwości, które są podane w zmiennej.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Właściwości SoftwareUpdateConfigurationRunContext

|Właściwość  |Opis  |
|---------|---------|
|Nazwa konfiguracji oprogramowania     | Nazwa konfiguracji aktualizacji oprogramowania.        |
|Identyfikator SoftwareUpdateConfigurationRunId     | Unikatowy identyfikator biegu.        |
|Instalacje konfiguracji softwareupdateconfigurations     | Kolekcja właściwości związanych z konfiguracją aktualizacji oprogramowania.         |
|OprogramowanieUpdateConfigurationSettings.operatingSystem     | Systemy operacyjne przeznaczone do wdrożenia aktualizacji.         |
|SoftwareUpdateConfigurationSettings.duration     | Maksymalny czas trwania wdrożenia `PT[n]H[n]M[n]S` aktualizacji jest uruchamiany zgodnie z iso8601; zwane również *oknem konserwacji*.          |
|OprogramowanieUpdateConfigurationSettings.Windows     | Kolekcja właściwości związanych z komputerami z systemem Windows.         |
|OprogramowanieUpdateConfigurationSettings.Windows.excludedKbNumbers     | Lista kbc, które są wykluczone z wdrożenia aktualizacji.        |
|OprogramowanieUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Aktualizuj klasyfikacje wybrane dla wdrożenia aktualizacji.        |
|OprogramowanieUpdateConfigurationSettings.Windows.rebootSetting     | Ustawienia ponownego uruchomienia wdrożenia aktualizacji.        |
|azureVirtualMachines     | Lista identyfikatorów zasobów dla maszyn wirtualnych platformy Azure we wdrożeniu aktualizacji.        |
|nonAzureComputerNames|Lista sieci FQDN komputerów innych niż platformy Azure we wdrożeniu aktualizacji.|

Poniższy przykład jest ciąg JSON przekazywane do **Parametru SoftwareUpdateConfigurationRunContext:**

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

Pełny przykład ze wszystkimi właściwościami można znaleźć na stronie: [Pobierz konfigurację aktualizacji oprogramowania według nazwy](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> Obiekt `SoftwareUpdateConfigurationRunContext` może zawierać zduplikowane wpisy dla maszyn. Może to spowodować, że skrypty wstępne i post-do uruchamiania wiele razy na tym samym komputerze. Aby obejść to `Sort-Object -Unique` zachowanie, należy wybrać tylko unikatowe nazwy maszyn wirtualnych w skrypcie.


## <a name="stopping-a-deployment"></a>Zatrzymywanie wdrożenia

Jeśli chcesz zatrzymać wdrożenie na podstawie skryptu wstępnego, należy [zgłosić](automation-runbook-execution.md#throw) wyjątek. Jeśli nie, wdrożenie i skrypt po będzie nadal działać. Poniższy fragment kodu pokazuje, jak zgłosić wyjątek.

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

## <a name="samples"></a>Samples

Przykłady skryptów wstępnych i posmarzowych można znaleźć w [Galerii Centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) i [galerii programu PowerShell](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)lub zaimportować je za pośrednictwem witryny Azure portal. Aby to zrobić, na koncie automatyzacji, w obszarze **Automatyzacja procesów**wybierz **galerię śmiób**. Użyj **zarządzania aktualizacjami** dla filtru.

![Lista galerii](./media/pre-post-scripts/runbook-gallery.png)

Możesz też wyszukać je według ich nazwy skryptu, jak pokazano na poniższej liście:

* Zarządzanie aktualizacjami — włączanie maszyn wirtualnych
* Zarządzanie aktualizacjami — wyłączanie maszyn wirtualnych
* Zarządzanie aktualizacjami — uruchamianie skryptu lokalnie
* Zarządzanie aktualizacjami — szablon dla skryptów przed/postem
* Zarządzanie aktualizacjami — uruchom skrypt za pomocą polecenia Uruchom

> [!IMPORTANT]
> Po zaimportowaniu ślikmy runbook, należy opublikować je, zanim będą mogły być używane. Aby to zrobić, znajdź program runbook na koncie automatyzacji, wybierz pozycję **Edytuj**, a następnie wybierz pozycję **Publikuj**.

Wszystkie przykłady są oparte na szablonie podstawowym, który jest zdefiniowany w poniższym przykładzie. Ten szablon może służyć do tworzenia własnego śmigła do użycia z przed i po skryptach. Logika niezbędne do uwierzytelniania za `SoftwareUpdateConfigurationRunContext` pomocą platformy Azure i obsługi parametru jest uwzględniony.

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

## <a name="interacting-with-machines"></a>Interakcja z maszynami

Zadania przed i po uruchomieniu są uruchamiane jako projekt uruchamiany na koncie automatyzacji, a nie bezpośrednio na komputerach we wdrożeniu. Zadania wstępne i po uruchomieniu również w kontekście platformy Azure i nie mają dostępu do maszyn innych niż azure. W poniższych sekcjach pokazano, jak można bezpośrednio wchodzić w interakcje z maszynami, niezależnie od tego, czy są to maszyny wirtualne platformy Azure, czy maszyny platformy non-Azure.

### <a name="interacting-with-azure-machines"></a>Interakcja z komputerami platformy Azure

Zadania wstępne i po są uruchamiane jako podręczniki runbook i nie są natywnie uruchamiane na maszynach wirtualnych platformy Azure we wdrożeniu. Aby wchodzić w interakcje z maszynami wirtualnymi platformy Azure, musisz mieć następujące elementy:

* Konto Uruchom jako
* Runbook, który chcesz uruchomić

Aby wchodzić w interakcje z maszynami platformy Azure, należy użyć polecenia cmdlet [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) do interakcji z maszynami wirtualnymi platformy Azure. Na przykład, jak to zrobić, zobacz przykładowy element zarządzania [aktualizacjami](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)w yskusuj — uruchom skrypt za pomocą polecenia Uruchom .

### <a name="interacting-with-non-azure-machines"></a>Interakcja z komputerami nienawiązanych z platformą Azure

Zadania przed i po uruchomieniu w kontekście platformy Azure i nie mają dostępu do maszyn innych niż azure. Aby wchodzić w interakcje z komputerami spoza platformy Azure, musisz mieć następujące elementy:

* Konto Uruchom jako
* Hybrydowy pracownik eksbrytyjny zainstalowany na komputerze
* System runbook, który chcesz uruchomić lokalnie
* Element runbook nadrzędny

Aby wchodzić w interakcje z komputerami spoza platformy Azure, nadrzędny element runbook jest uruchamiany w kontekście platformy Azure. Ten zestaw runbook wywołuje podrzędny system runbook z polecenia cmdlet [Start-AzureRmAutomationRunbook.](/powershell/module/azurerm.automation/start-azurermautomationrunbook) Należy określić `-RunOn` parametr i podać nazwę hybrydowego procesu roboczego uruchomieniu dla skryptu, na który ma być uruchomiony. Aby uzyskać więcej informacji, zobacz przykładowy program Zarządzania aktualizacjami w [yskusuj — uruchamia skrypt lokalnie](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Przerwanie wdrażania poprawek

Jeśli skrypt wstępny zwraca błąd, można przerwać wdrożenie. Aby to zrobić, należy [zgłosić](/powershell/module/microsoft.powershell.core/about/about_throw) błąd w skrypcie dla dowolnej logiki, która stanowiłaby błąd.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>Znane problemy

* Nie można przekazać wartości logicznej, obiektów lub tablic do parametrów podczas korzystania ze skryptów wstępnych i post-. Jeśli to zrobisz, system runbook zakończy się niepowodzeniem. Aby uzyskać pełną listę obsługiwanych typów, zobacz [Przekazywanie parametrów](#passing-parameters).

## <a name="next-steps"></a>Następne kroki

Przejdź do następującego samouczka, aby dowiedzieć się, jak zarządzać aktualizacjami maszyn wirtualnych systemu Windows:

> [!div class="nextstepaction"]
> [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows na platformie Azure](automation-tutorial-update-management.md)

