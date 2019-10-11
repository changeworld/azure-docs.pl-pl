---
title: Kompilowanie konfiguracji w konfiguracji stanu Azure Automation
description: W tym artykule opisano, jak kompilować konfiguracje konfiguracji żądanego stanu (DSC) dla Azure Automation.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 09/10/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3d9c6c9b73f8887d4fdb85da277b2e27d8f5221c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243567"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Kompilowanie konfiguracji DSC w konfiguracji stanu Azure Automation

Konfiguracje konfiguracji żądanego stanu (DSC) można kompilować na dwa sposoby z konfiguracją stanu Azure Automation: na platformie Azure i w środowisku Windows PowerShell. Poniższa tabela ułatwia określenie, kiedy należy używać metody opartej na charakterystyce każdej z nich:

- Usługa kompilacji konfiguracji stanu platformy Azure
  - Metoda początkująca z interakcyjnym interfejsem użytkownika
   - Łatwe śledzenie stanu zadania

- Windows PowerShell
  - Wywołanie z programu Windows PowerShell na lokalnej stacji roboczej lub usłudze kompilacji
  - Integracja z potokiem testów programistycznych
  - Podaj złożone wartości parametrów
  - Pracuj z danymi węzła i niebędącymi węzłami w skali
  - Znaczne zwiększenie wydajności

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Kompilowanie konfiguracji DSC w konfiguracji stanu platformy Azure

### <a name="portal"></a>Portal

1. Na koncie usługi Automation kliknij pozycję **Konfiguracja stanu (DSC)** .
1. Kliknij kartę **konfiguracje** , a następnie kliknij nazwę konfiguracji do skompilowania.
1. Kliknij przycisk **Kompiluj**.
1. Jeśli konfiguracja nie ma żadnych parametrów, zostanie wyświetlony monit o potwierdzenie, czy chcesz go skompilować. Jeśli konfiguracja zawiera parametry, zostanie otwarty blok **kompilowania konfiguracji** , w którym można podać wartości parametrów. Więcej informacji o parametrach można znaleźć w sekcji [**podstawowe parametry**](#basic-parameters) .
1. Zostanie otwarta strona **zadanie kompilacji** , dzięki czemu można śledzić stan zadania kompilacji, a także konfiguracje węzłów (dokumenty konfiguracji MOF), które zostały wykonane na serwerze ściągania konfiguracji stanu Azure Automation.

### <a name="azure-powershell"></a>Program Azure PowerShell

Aby rozpocząć Kompilowanie za pomocą programu Windows PowerShell, można użyć [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) . Następujący przykładowy kod uruchamia kompilację konfiguracji DSC o nazwie **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzureRmAutomationDscCompilationJob` zwraca obiekt zadania kompilacji, którego można użyć do śledzenia stanu. Następnie można użyć tego obiektu zadania kompilacji z [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob)
Aby określić stan zadania kompilacji i [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput)
, aby wyświetlić jego strumienie (dane wyjściowe). Następujący przykładowy kod uruchamia kompilację konfiguracji **SampleConfig** , czeka, dopóki nie zostanie ukończona, a następnie wyświetla jej strumienie.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

###  <a name="basic-parameters"></a>Podstawowe parametry

Deklaracja parametru w konfiguracjach DSC, w tym typy parametrów i właściwości, działa tak samo jak w Azure Automation elementach Runbook. Zobacz [Uruchamianie elementu Runbook w Azure Automation](automation-starting-a-runbook.md) , aby dowiedzieć się więcej na temat parametrów elementu Runbook.

W poniższym przykładzie zastosowano dwa parametry o nazwie **FeatureName** i **isobecny**, aby określić wartości właściwości w konfiguracji węzła **ParametersExample. Samples** , generowanej podczas kompilacji.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Konfiguracje DSC wykorzystujące podstawowe parametry można skompilować w portalu konfiguracji stanu Azure Automation lub z Azure PowerShell:

#### <a name="portal"></a>Portal

W portalu możesz wprowadzić wartości parametrów po kliknięciu przycisku **Kompiluj**.

![Parametry kompilacji konfiguracji](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Program Azure PowerShell

Program PowerShell wymaga parametrów w elemencie [Hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables) , gdzie klucz jest zgodny z nazwą parametru, a wartość jest równa wartości parametru.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Aby uzyskać informacje o przekazywaniu PSCredentials jako parametrów, zobacz poniższe [zasoby poświadczeń](#credential-assets) .

### <a name="compiling-configurations-in-azure-automation-that-contain-composite-resources"></a>Kompilowanie konfiguracji w Azure Automation zawierających zasoby złożone

**Zasoby złożone** umożliwiają używanie konfiguracji DSC jako zasobów zagnieżdżonych wewnątrz konfiguracji. Dzięki temu można zastosować wiele konfiguracji do pojedynczego zasobu. Zobacz [zasoby złożone: użycie konfiguracji DSC jako zasobu,](/powershell/scripting/dsc/resources/authoringresourcecomposite) aby dowiedzieć się więcej na temat **zasobów złożonych**.

> [!NOTE]
> Aby konfiguracje zawierające **zasoby złożone** zostały prawidłowo skompilowane, należy najpierw upewnić się, że wszystkie zasoby DSC, na których bazują złożone, są najpierw importowane do Azure Automation.

Dodawanie **zasobu złożonego** DSC nie jest inne niż dodanie modułu programu PowerShell do Azure Automation.
Instrukcje krok po kroku dotyczące tego procesu opisano w artykule [Zarządzanie modułami w Azure Automation](/azure/automation/shared-resources/modules).

### <a name="managing-configurationdata-when-compiling-configuration-in-azure-automation"></a>Zarządzanie ConfigurationData podczas kompilowania konfiguracji w Azure Automation

**ConfigurationData** pozwala oddzielić konfigurację strukturalną od wszelkich konfiguracji specyficznych dla środowiska podczas korzystania z programu PowerShell DSC. Aby dowiedzieć się więcej na temat **ConfigurationData**, zobacz [rozdzielenie "co" od "gdzie" w programie PowerShell DSC](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) .

> [!NOTE]
> **ConfigurationData** można użyć podczas kompilowania konfiguracji stanu Azure Automation przy użyciu Azure PowerShell, ale nie w Azure Portal.

Poniższa przykładowa Konfiguracja DSC używa **ConfigurationData** za pomocą słów kluczowych **$ConfigurationData** i **$AllNodes** . Wymagany jest również moduł [ **xWebAdministration** ](https://www.powershellgallery.com/packages/xWebAdministration/) dla tego przykładu:

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

Poprzednią konfigurację DSC można skompilować za pomocą programu Windows PowerShell. Poniższy skrypt dodaje dwa konfiguracje węzłów do usługi ściągania konfiguracji stanu Azure Automation: **ConfigurationDataSample. MyVM1** i **ConfigurationDataSample. MyVM3**:

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="working-with-assets-in-azure-automation-during-compilation"></a>Praca z zasobami w Azure Automation podczas kompilacji

Odwołania do elementów zawartości są takie same w konfiguracji stanu Azure Automation i elementów Runbook. Aby uzyskać więcej informacji, zobacz następujące tematy:

- [Certyfikaty](automation-certificates.md)
- [Połączenia](automation-connections.md)
- [Poświadczenia](automation-credentials.md)
- [Zmienne](automation-variables.md)

#### <a name="credential-assets"></a>Zasoby poświadczeń

Konfiguracje DSC w Azure Automation mogą odwoływać się do zasobów poświadczeń usługi Automation przy użyciu polecenia cmdlet `Get-AutomationPSCredential`. Jeśli konfiguracja ma parametr, który ma typ **PSCredential** , można użyć polecenia cmdlet `Get-AutomationPSCredential`, przekazując nazwę ciągu Azure Automation zasobu poświadczenia do polecenia cmdlet, aby pobrać poświadczenie. Następnie można użyć tego obiektu dla parametru wymagającego obiektu **PSCredential** . W tle zasób poświadczeń Azure Automation o tej nazwie zostanie pobrany i przesłany do konfiguracji. W poniższym przykładzie pokazano to w działaniu.

Utrzymywanie bezpiecznych poświadczeń w konfiguracjach węzłów (dokumenty konfiguracji MOF) wymaga szyfrowania poświadczeń w pliku MOF konfiguracji węzła. Jednak obecnie należy poinformować program PowerShell DSC, aby poświadczenia zostały wygenerowane w postaci zwykłego tekstu podczas generowania pliku MOF konfiguracji węzła, ponieważ Konfiguracja DSC programu PowerShell nie wie, że Azure Automation będzie szyfrować cały plik MOF po jego generowaniu za pośrednictwem zadania kompilacji.

Możesz poinformować, że program PowerShell DSC ma być w stanie wypełniania poświadczeń w postaci zwykłego tekstu w wygenerowanej konfiguracji węzła pliki MOF przy użyciu danych konfiguracyjnych. Należy przekazać `PSDscAllowPlainTextPassword = $true` za pośrednictwem **ConfigurationData** dla każdego bloku węzła, który pojawia się w konfiguracji DSC i używa poświadczeń.

W poniższym przykładzie przedstawiono konfigurację DSC korzystającą z zasobu poświadczenia usługi Automation.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

Poprzednią konfigurację DSC można skompilować za pomocą programu PowerShell. Poniższy program PowerShell dodaje dwa konfiguracje węzłów do serwera ściągania konfiguracji stanu Azure Automation: **CredentialSample. MyVM1** i **CredentialSample. MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Po zakończeniu kompilacji może zostać wyświetlony komunikat o błędzie: **moduł "Microsoft. PowerShell. Management" nie został zaimportowany, ponieważ Przystawka "Microsoft. PowerShell. Management" została już zaimportowana.** To ostrzeżenie można bezpiecznie zignorować.

## <a name="compiling-configurations-in-windows-powershell-and-publishing-to-azure-automation"></a>Kompilowanie konfiguracji w programie Windows PowerShell i publikowanie w Azure Automation

Można również zaimportować konfiguracje węzłów (pliki MOF), które zostały skompilowane poza platformą Azure.
Obejmuje to Kompilowanie z poziomu stacji roboczej deweloperów lub usługi, takiej jak [Azure DevOps](https://dev.azure.com).
Istnieje wiele zalet tego podejścia, w tym wydajność i niezawodność.
Kompilacja w programie Windows PowerShell udostępnia również opcję podpisywania zawartości konfiguracji.
Konfiguracja węzła podpisanego jest weryfikowana lokalnie w zarządzanym węźle przez agenta DSC, co zapewnia, że konfiguracja stosowana do węzła pochodzi z autoryzowanego źródła.

> [!NOTE]
> Plik konfiguracji węzła nie może być większy niż 1 MB, aby można go było zaimportować do Azure Automation.

Więcej informacji o sposobach podpisywania konfiguracji węzłów znajduje się [w temacie ulepszenia w programie WMF 5,1 — Jak podpisać konfigurację i moduł](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compiling-a-configuration-in-windows-powershell"></a>Kompilowanie konfiguracji w programie Windows PowerShell

Proces kompilowania konfiguracji DSC w programie Windows PowerShell jest dostępny w dokumentacji DSC programu PowerShell [pisanie, kompilowanie i stosowanie konfiguracji](/powershell/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Można to zrobić z poziomu stacji roboczej dewelopera lub w ramach usługi kompilacji, takiej jak [Azure DevOps](https://dev.azure.com).

Plik MOF lub pliki generowane przez skompilowanie konfiguracji można następnie zaimportować bezpośrednio do usługi Azure State Configuration.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importowanie konfiguracji węzła w Azure Portal

1. Na koncie usługi Automation kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie **Konfiguracja stanu (DSC)** kliknij kartę **konfiguracje** , a następnie kliknij pozycję **+ Dodaj**.
1. Na stronie **Importowanie** kliknij ikonę folderu obok pola tekstowego **plik konfiguracji węzła** , aby wyszukać plik konfiguracji węzła (MOF) na komputerze lokalnym.

   ![Przeglądaj w poszukiwaniu pliku lokalnego](./media/automation-dsc-compile/import-browse.png)

1. Wprowadź nazwę w polu tekstowym **nazwa konfiguracji** . Ta nazwa musi być zgodna z nazwą konfiguracji, z której została skompilowana konfiguracja węzła.
1. Kliknij przycisk **OK**.

### <a name="importing-a-node-configuration-with-azure-powershell"></a>Importowanie konfiguracji węzła przy użyciu Azure PowerShell

Możesz użyć polecenia cmdlet [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) , aby zaimportować konfigurację węzła do konta usługi Automation.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć, zobacz [wprowadzenie do konfiguracji stanu Azure Automation](automation-dsc-getting-started.md)
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC, aby można było przypisać je do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu Azure Automation](automation-dsc-compile.md)
- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz temat [polecenia cmdlet konfiguracji stanu Azure Automation](/powershell/module/azurerm.automation/#automation)
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Aby zapoznać się z przykładem użycia konfiguracji stanu Azure Automation w potoku ciągłego wdrażania, zobacz [wdrażanie ciągłe przy użyciu konfiguracji stanu Azure Automation i czekolady](automation-dsc-cd-chocolatey.md)
