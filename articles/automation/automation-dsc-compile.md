---
title: Kompilowanie konfiguracji DSC automatyzacji Azure
description: W tym artykule opisano, jak skompilować konfiguracje konfiguracji żądanego stanu (DSC) dla usługi Automatyzacja Azure.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: b085a75acc4d7744587f0c93482435b5b8c28fc2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="compiling-configurations-in-azure-automation-dsc"></a>Kompilowanie konfiguracji DSC automatyzacji Azure

Konfiguracje konfiguracji żądanego stanu (DSC) na dwa sposoby automatyzacji Azure można kompilować: w portalu Azure i przy użyciu programu Windows PowerShell. Poniższa tabela ułatwia określenie, kiedy należy używać które metody w zależności od właściwości każdej:

### <a name="azure-portal"></a>Azure Portal

* Najprostszą metodą z interaktywnego interfejsu użytkownika
* Formularz, aby podać wartości parametrów proste
* Łatwo śledzić stan zadania
* Dostęp uwierzytelniony dzięki funkcji logowania do platformy Azure

### <a name="windows-powershell"></a>Windows PowerShell

* Wywoływanie z wiersza polecenia za pomocą poleceń cmdlet środowiska Windows PowerShell
* Może być uwzględniony w zautomatyzowane rozwiązanie z wielu kroków
* Podaj wartości parametrów proste i złożone
* Śledź stan zadania
* Klient wymagane do obsługi poleceń cmdlet programu PowerShell
* ConfigurationData — dostęp próbny
* Kompiluj konfiguracje, które używają poświadczeń

Po podjęciu decyzji dotyczącej Metoda kompilacji, należy użyć poniższych procedur można uruchomić kompilacji.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Kompilowanie konfiguracji DSC z portalu Azure

1. Twoje konto usługi Automatyzacja kliknij **konfiguracji DSC**.
2. Kliknij pozycję Konfiguracja, aby otworzyć jego blok.
3. Kliknij przycisk **skompilować**.
4. Jeśli konfiguracja nie ma parametrów, monit o potwierdzenie, czy chcesz go skompilować. Jeśli konfiguracja ma parametry, **skompilować konfiguracji** zostanie otwarty blok, więc można podać wartości parametrów. Zobacz następujące tematy [ **podstawowe parametry** ](#basic-parameters) sekcji, aby uzyskać więcej informacji o parametrach.
5. **Zadania kompilacji** bloku jest otwarty, dzięki czemu można śledzić stan zadania kompilacji i konfiguracje węzłów (MOF konfiguracji dokumenty) powodowała ona być umieszczone na serwerze ściągania usługi Konfiguracja DSC automatyzacji Azure.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Kompilowanie konfiguracji DSC, przy użyciu programu Windows PowerShell

Można użyć [ `Start-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) zacząć od kompilowania przy użyciu programu Windows PowerShell. Następujący przykładowy kod uruchamia kompilacji konfiguracji DSC o nazwie **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
```

`Start-AzureRmAutomationDscCompilationJob` Zwraca obiekt zadania kompilacji, który można śledzić jego stan. Następnie można użyć tego obiektu zadania kompilacji [ `Get-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) do określenia stanu zadania kompilacji i [ `Get-AzureRmAutomationDscCompilationJobOutput` ](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput) Aby wyświetlić jego strumieni (dane wyjściowe). Następujący przykładowy kod uruchamia kompilacji **SampleConfig** konfiguracji, czeka, dopóki nie zostało ukończone, a następnie wyświetla jego strumieni.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>Podstawowe parametry
Deklaracji parametru w konfiguracji DSC, w tym typy parametrów i właściwości, działa tak samo, jak elementy runbook automatyzacji Azure. Zobacz [uruchamianie elementu runbook automatyzacji Azure](automation-starting-a-runbook.md) Aby dowiedzieć się więcej na temat parametrów elementu runbook.

W poniższym przykładzie użyto dwóch parametrów o nazwie **FeatureName** i **IsPresent**, aby ustalić wartości właściwości w **ParametersExample.sample** konfiguracji węzła, generowanych podczas kompilacji.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]

        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = "Present"
    if($IsPresent -eq $false)
    {
        $EnsureString = "Absent"
    }

    Node "sample"
    {
        WindowsFeature ($FeatureName + "Feature")
        {
            Ensure = $EnsureString
            Name = $FeatureName
        }
    }
}
```

Można skompilować konfiguracji DSC, używanego podstawowych parametrów w portalu usługi Konfiguracja DSC automatyzacji Azure lub za pomocą programu Azure PowerShell:

### <a name="portal"></a>Portal

W portalu, można wprowadzić wartości parametrów po kliknięciu przycisku **skompilować**.

![tekst alternatywny](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

PowerShell wymaga parametrów w [hashtable](http://technet.microsoft.com/library/hh847780.aspx) gdzie klucz jest zgodna z nazwą parametru, a wartość jest równa wartości parametru.

```powershell
$Parameters = @{
    "FeatureName" = "Web-Server"
    "IsPresent" = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters
```

Aby dowiedzieć się, jak przekazywanie PSCredentials jako parametrów, zobacz <a href="#credential-assets"> **zasoby poświadczeń** </a> poniżej.

## <a name="composite-resources"></a>Złożone zasobów

**Złożone zasobów** umożliwiają używanie konfiguracji DSC jako zagnieżdżonych zasobów w ramach konfiguracji. Dzięki temu można zastosować konfiguracji z wieloma do pojedynczego zasobu. Zobacz [zasobów złożonego: przy użyciu konfiguracji DSC jako zasób](https://docs.microsoft.com/powershell/dsc/authoringresourcecomposite) Aby dowiedzieć się więcej o **złożonego zasobów**

> [!NOTE]
> Aby **złożonego zasobów** skompilować poprawnie, należy najpierw upewnić czy wszystkie zasoby DSC, które złożone opiera się na pierwszej instalacji w repozytorium modułów konto usługi Automatyzacja Azure lub nie importuje poprawnie.

Aby dodać DSC **złożonego zasobów**, należy dodać moduł zasobów do archiwum (* .zip). Przejdź do repozytorium modułów na Twoje konto usługi Automatyzacja Azure. Kliknij przycisk "Dodaj moduł".

![Dodaj moduł](./media/automation-dsc-compile/add_module.png)

Przejdź do katalogu, w którym znajduje się archiwum. Wybierz plik archiwum, a następnie kliknij przycisk OK.

![Wybierz moduł](./media/automation-dsc-compile/select_dscresource.png)

Nastąpi powrót do katalogu moduły, w którym można monitorować stan programu **złożonego zasobów** podczas wypakowuje rejestruje w usłudze Automatyzacja Azure.

![Importuj zasób złożone](./media/automation-dsc-compile/register_composite_resource.png)

Po zarejestrowaniu modułu można następnie kliknięciu go, aby sprawdzić, czy **złożonego zasobów** są teraz dostępne do użycia w konfiguracji.

![Sprawdź poprawność złożonego zasobów](./media/automation-dsc-compile/validate_composite_resource.png)

Następnie można wywołać **złożonego zasobów** do konfiguracji w następujący sposób:

```powershell

    Node ($AllNodes.Where{$_.Role -eq "WebServer"}).NodeName
    {
            
            JoinDomain DomainJoin
            {
                DomainName = $DomainName
                Admincreds = $Admincreds
            }

            PSWAWebServer InstallPSWAWebServer
            {
                DependsOn = "[JoinDomain]DomainJoin"
            }        
    }

```

## <a name="configurationdata"></a>ConfigurationData
**ConfigurationData** umożliwia rozdzielenie strukturalnych konfigurację z żadnej konfiguracji określonego środowiska podczas korzystania z usługi Konfiguracja DSC środowiska PowerShell. Zobacz [oddzielenie "Co" od "Where" w konfiguracji DSC środowiska PowerShell](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) Aby dowiedzieć się więcej o **ConfigurationData**.

> [!NOTE]
> Można użyć **ConfigurationData** podczas kompilowania w konfiguracji DSC automatyzacji Azure przy użyciu programu Azure PowerShell, ale nie w portalu Azure.

Poniższa przykładowa konfiguracja DSC konfiguracja używa **ConfigurationData** za pośrednictwem **$ConfigurationData** i **$AllNodes** słów kluczowych. Należy również [ **xWebAdministration** modułu](https://www.powershellgallery.com/packages/xWebAdministration/) w tym przykładzie:

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    {
        xWebsite Site
        {
            Name = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure   = "Present"
        }
    }
}
```

Można kompilować tej konfiguracji DSC przy użyciu programu PowerShell. Następujące środowiska PowerShell dodaje dwie konfiguracje węzłów na serwerze ściągania usługi Konfiguracja DSC automatyzacji Azure: **ConfigurationDataSample.MyVM1** i **ConfigurationDataSample.MyVM3**:

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "MyVM1"
            Role = "WebServer"
        },
        @{
            NodeName = "MyVM2"
            Role = "SQLServer"
        },
        @{
            NodeName = "MyVM3"
            Role = "WebServer"
        }
    )

    NonNodeData = @{
        SomeMessage = "I love Azure Automation DSC!"
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData
```

## <a name="assets"></a>Elementy zawartości

Odwołania do zasobów są takie same, w konfiguracji DSC automatyzacji Azure i elementów runbook. Zobacz następujące tematy, aby uzyskać więcej informacji:

* [Certyfikaty](automation-certificates.md)
* [Połączenia](automation-connections.md)
* [Poświadczenia](automation-credentials.md)
* [Zmienne](automation-variables.md)

### <a name="credential-assets"></a>Zasoby poświadczeń

Podczas konfiguracji DSC automatyzacji Azure mogą odwoływać się zasoby poświadczeń przy użyciu **Get-AutomationPSCredential**, zasoby poświadczeń może również zostać przekazane za za pomocą parametrów, w razie potrzeby. Jeśli konfiguracja przyjmuje parametr **PSCredential** typ, a następnie trzeba przekazać nazwę ciągu elementu zasób poświadczenia usługi Automatyzacja Azure jako wartość tego parametru, a nie obiektu PSCredential. W tle zasób poświadczeń usługi Automatyzacja Azure o tej nazwie jest pobierany i przekazywane do konfiguracji.

Przechowywanie poświadczeń zabezpieczone w konfiguracji węzłów (MOF konfiguracji dokumenty) wymaga szyfrowania poświadczeń w pliku MOF konfiguracji węzła. Automatyzacja Azure dalsze przyjmuje jednym kroku i szyfrowanie całego pliku MOF. Jednak obecnie musi wskazujemy DSC środowiska PowerShell jest poprawny dla poświadczeń do wyprodukowania w postaci zwykłego tekstu podczas generowania MOF konfiguracji węzła, ponieważ DSC programu PowerShell nie może ustalić, czy usługi Automatyzacja Azure będzie można szyfrowanie całego pliku MOF po jej generacji za pomocą zadania kompilacji.

Można ustalić DSC środowiska PowerShell, że poświadczenia do wyprodukowania w postaci zwykłego tekstu w konfiguracji węzła wygenerowane za pomocą [ **ConfigurationData**](#configurationdata). Należy przekazać `PSDscAllowPlainTextPassword = $true` za pośrednictwem **ConfigurationData** dla każdego węzła bloku nazwę, która pojawia się w konfiguracji DSC i używa poświadczeń.

Poniższy przykład przedstawia konfiguracji DSC, która używa zasób poświadczenia usługi Automatyzacja.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential "SomeCredentialAsset"

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath = "\\Server\share\path\file.ext"
            DestinationPath = "C:\destinationPath"
            Credential = $Cred
        }
    }
}
```

Można kompilować tej konfiguracji DSC przy użyciu programu PowerShell. Następujące środowiska PowerShell dodaje dwie konfiguracje węzłów na serwerze ściągania usługi Konfiguracja DSC automatyzacji Azure: **CredentialSample.MyVM1** i **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "*"
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = "MyVM1"
        },
        @{
            NodeName = "MyVM2"
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData
```

> [!NOTE]
> Po zakończeniu kompilacji może zostać wyświetlony błąd informujący o: **modułu "Microsoft.PowerShell.Management" nie został zaimportowany, ponieważ przystawka "Microsoft.PowerShell.Management" została już zaimportowana.** Można bezpiecznie zignorować to ostrzeżenie.

## <a name="importing-node-configurations"></a>Importowanie konfiguracji węzła

Możesz również zaimportować konfiguracje węzłów (za), które ma być kompilowana poza platformą Azure. Jedną z zalet tego jest konfiguracje węzłów może być podpisana.
Konfiguracja węzła podpisem jest weryfikowany lokalnie w węźle zarządzanym przez agenta DSC, zapewniając, że konfiguracji są stosowane do węzeł pochodzi z autoryzowanego źródła.

> [!NOTE]
> Można użyć importu podpisany konfiguracji do konta usługi Automatyzacja Azure, ale automatyzacji Azure nie obsługuje obecnie kompilowanie konfiguracje podpisem.

> [!NOTE]
> Plik konfiguracji węzła nie może być większe niż 1 MB, aby umożliwić jego do zaimportowania do automatyzacji Azure.

Znajdują się informacje dotyczące podpisywania konfiguracje węzłów w https://msdn.microsoft.com/powershell/wmf/5.1/dsc-improvements#how-to-sign-configuration-and-module.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importowanie konfiguracji węzła, w portalu Azure

1. Twoje konto usługi Automatyzacja kliknij **konfiguracji węzłów DSC** w obszarze **zarządzania konfiguracją**.

    ![Konfiguracje węzłów DSC](./media/automation-dsc-compile/node-config.png)
2. W **konfiguracji węzłów DSC** bloku, kliknij przycisk **dodać NodeConfiguration**.
3. W **importu** bloku, kliknij ikonę folderu **pliku konfiguracji węzła** pole tekstowe, aby wyszukać plik konfiguracji węzła (MOF) na komputerze lokalnym.

    ![Przeglądaj w poszukiwaniu pliku lokalnego](./media/automation-dsc-compile/import-browse.png)
4. Wprowadź nazwę w **Nazwa konfiguracji** pola tekstowego. Ta nazwa musi odpowiadać nazwie konfiguracji, z którego został skompilowany konfiguracji węzła.
5. Kliknij przycisk **OK**.

### <a name="importing-a-node-configuration-with-powershell"></a>Importowanie konfiguracji węzła, przy użyciu programu PowerShell

Można użyć [AzureRmAutomationDscNodeConfiguration importu](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) polecenia cmdlet do importowania konfiguracji węzła na koncie automatyzacji.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName "MyAutomationAccount" -ResourceGroupName "MyResourceGroup" -ConfigurationName "MyNodeConfiguration" -Path "C:\MyConfigurations\TestVM1.mof"
```



