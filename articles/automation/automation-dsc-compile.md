---
title: Kompilowanie konfiguracji w konfiguracji stan automatyzacji platformy Azure
description: W tym artykule opisano sposób kompilowania konfiguracji usługi Azure Automation Desired State Configuration (DSC).
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 09/10/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 847c928681451b4fef93198e2f2272d5bb04b1b8
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919800"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Kompilowanie konfiguracji DSC usługi Azure Automation stanu konfiguracji

Można kompilować konfiguracje Desired State Configuration (DSC) na dwa sposoby, za pomocą usługi Azure Automation stan konfiguracji: w witrynie Azure portal i za pomocą programu Windows PowerShell. Poniższa tabela ułatwia określenie, kiedy należy użyć metody, które na podstawie charakterystyki każdej:

**Azure Portal**

- Najprostszą metodą z interaktywnego interfejsu użytkownika
- Formularz, aby podać wartości parametrów prosty
- Łatwo śledzić stan zadania
- Dostęp uwierzytelniony za pomocą logowania platformy Azure

**Windows PowerShell**

- Wywoływanie z wiersza polecenia za pomocą poleceń cmdlet programu Windows PowerShell
- Mogą być zawarte w zautomatyzowane rozwiązanie z wieloma krokami
- Podaj wartości parametrów proste i złożone
- Śledzić stan zadania
- Klient wymagane do obsługi poleceń cmdlet programu PowerShell
- Przekazywanie danych konfiguracji
- Kompilowanie konfiguracji, które używają poświadczeń

Po użytkownik zdecydował się na metodzie, kompilacji, należy użyć poniższych procedur można uruchomić kompilacji.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Kompilowanie konfiguracji DSC w witrynie Azure portal

1. Na koncie usługi Automation kliknij **State configuration (DSC)**.
1. Kliknij pozycję **konfiguracje** , a następnie kliknij nazwę konfiguracji, aby skompilować.
1. Kliknij przycisk **skompilować**.
1. Jeśli konfiguracja nie ma parametrów, pojawia się monit o potwierdzenie, czy chcesz go skompilować. Jeśli konfiguracja ma parametry, **kompilowania konfiguracji** zostanie otwarty blok, dzięki czemu możesz podać wartości parametrów. Zapoznaj się z poniższymi [ **podstawowych parametrów** ](#basic-parameters) sekcji, aby uzyskać więcej szczegółowych informacji dotyczących parametrów.
1. **Zadanie kompilacji** tak, aby śledzić stan zadania kompilacji i konfiguracje węzłów (pliku MOF konfiguracji dokumenty), powodowała ona być umieszczone na serwerze usługi Azure Automation stan konfiguracji ściągania zostanie otwarta strona.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Kompilowanie konfiguracji DSC przy użyciu programu Windows PowerShell

Możesz użyć [ `Start-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) można uruchomić kompilacji za pomocą programu Windows PowerShell. Następujący przykładowy kod uruchamia kompilacji konfiguracji DSC, o nazwie **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzureRmAutomationDscCompilationJob` Zwraca obiekt zadania kompilacji za pomocą którego można użyć, aby śledzić jego stan. Następnie można użyć tego obiektu zadania kompilacji [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob)
do określenia stanu zadania kompilacji i [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput)
Aby wyświetlić jego strumieni (dane wyjściowe). Następujący przykładowy kod uruchamia kompilację **SampleConfig** konfiguracji, czeka, dopóki nie zostanie zakończona, a następnie wyświetla jego strumieni.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>Podstawowe parametry

Deklaracji parametru w konfiguracjach DSC, w tym typy parametrów i właściwości, działa tak samo, jak elementy runbook usługi Azure Automation. Zobacz [uruchamianie elementu runbook w usłudze Azure Automation](automation-starting-a-runbook.md) Aby dowiedzieć się więcej na temat parametrów elementu runbook.

W poniższym przykładzie użyto dwóch parametrów o nazwie **FeatureName** i **IsPresent**, aby określić wartości właściwości w **ParametersExample.sample** węzła Konfiguracja, które są generowane podczas kompilacji.

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

Można skompilować konfiguracji DSC, używanego przez podstawowe parametry w portalu usługi Azure Automation stan konfiguracji lub przy użyciu programu Azure PowerShell:

### <a name="portal"></a>Portal

W portalu, można wprowadzić wartości parametrów, po kliknięciu przycisku **skompilować**.

![Parametry kompilacji konfiguracji](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

Program PowerShell wymaga parametrów w [hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables) których klucz pasuje do nazwy parametru i wartość jest równa wartości parametru.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Aby uzyskać informacje o przekazywaniu PSCredentials jako parametry, zobacz [zasobów poświadczeń](#credential-assets) poniżej.

## <a name="composite-resources"></a>Zasoby złożone

**Zasoby złożone** umożliwiają używanie konfiguracji DSC jako zagnieżdżonych zasobów w ramach konfiguracji. Dzięki temu można zastosować konfiguracji z wieloma do pojedynczego zasobu. Zobacz [zasoby złożone: Przy użyciu konfiguracji DSC jako zasób](/powershell/dsc/authoringresourcecomposite) Aby dowiedzieć się więcej na temat **zasoby złożone**.

> [!NOTE]
> Aby **zasoby złożone** skompilować poprawnie, najpierw upewnij się, że wszystkie zasoby DSC, które złożonego opiera się na pierwszej instalacji w repozytorium modułów konta automatyzacji platformy Azure lub nie importuje prawidłowo.

Aby dodać DSC **złożonego zasobów**, moduł zasobów należy dodać do archiwum (* .zip). Przejdź do repozytorium modułów na konto usługi Azure Automation. Następnie kliknij przycisk "Dodaj moduł".

![Dodawanie modułu](./media/automation-dsc-compile/add_module.png)

Przejdź do katalogu, w którym znajduje się archiwum. Wybierz plik archiwum, a następnie kliknij przycisk OK.

![Wybierz moduł](./media/automation-dsc-compile/select_dscresource.png)

Nastąpi powrót do katalogu modułów, w którym można monitorować stan usługi **złożonego zasobów** wypakowuje, i rejestruje za pomocą usługi Azure Automation.

![Importuj zasób złożone](./media/automation-dsc-compile/register_composite_resource.png)

Po zarejestrowaniu modułu, możesz następnie kliknąć go, aby sprawdzić, czy **zasoby złożone** są teraz dostępne do użycia w konfiguracji.

![Sprawdź poprawność zasobów złożonych](./media/automation-dsc-compile/validate_composite_resource.png)

Następnie możesz wywołać **złożonego zasobów** do konfiguracji w następujący sposób:

```powershell
Node ($AllNodes.Where{$_.Role -eq 'WebServer'}).NodeName
{
    DomainConfig myCompositeConfig
    {
        DomainName = $DomainName
        Admincreds = $Admincreds
    }

    PSWAWebServer InstallPSWAWebServer
    {
        DependsOn = '[DomainConfig]myCompositeConfig'
    }
}
```

## <a name="configurationdata"></a>ConfigurationData

**ConfigurationData** umożliwia rozdzielenie strukturalnych konfiguracji z żadnej konfiguracji specyficznych dla środowiska, podczas korzystania z programu PowerShell DSC. Zobacz [oddzielenie "Co" od "Where" w programie PowerShell DSC](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) Aby dowiedzieć się więcej na temat **ConfigurationData**.

> [!NOTE]
> Możesz użyć **ConfigurationData** podczas kompilowania w konfiguracji stan automatyzacji platformy Azure przy użyciu programu Azure PowerShell, ale nie w witrynie Azure portal.

Poniższa przykładowa DSC konfiguracja używa **ConfigurationData** za pośrednictwem **$ConfigurationData** i **$AllNodes** słów kluczowych. Należy również [ **xWebAdministration** modułu](https://www.powershellgallery.com/packages/xWebAdministration/) w tym przykładzie:

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

Można kompilować tej konfiguracji DSC przy użyciu programu PowerShell. Następujące polecenie programu PowerShell dodaje dwie konfiguracje węzłów do serwera usługi Azure Automation stan konfiguracji ściągania: **ConfigurationDataSample.MyVM1** i **ConfigurationDataSample.MyVM3**:

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

## <a name="assets"></a>Elementy zawartości

Odwołania do zasobów są takie same, w konfiguracji stan automatyzacji platformy Azure i elementów runbook. Zapoznaj się z poniższymi, aby uzyskać więcej informacji:

- [Certyfikaty](automation-certificates.md)
- [Połączenia](automation-connections.md)
- [Poświadczenia](automation-credentials.md)
- [Zmienne](automation-variables.md)

### <a name="credential-assets"></a>Zasobów poświadczeń

Konfiguracje DSC w usłudze Azure Automation mogą odwoływać się do zasobów poświadczeń usługi Automation przy użyciu `Get-AutomationPSCredential` polecenia cmdlet. Jeśli konfiguracja ma parametr, który ma **PSCredential** typ, a następnie można użyć `Get-AutomationPSCredential` polecenia cmdlet, przekazując nazwę ciągu elementu zasób poświadczenia usługi Azure Automation do polecenia cmdlet, aby pobrać poświadczenie. Można użyć obiektu wymagająca parametr **PSCredential** obiektu. Za kulisami zasób poświadczenia usługi Azure Automation, o tej nazwie jest pobierane i przekazywane do konfiguracji. W poniższym przykładzie pokazano to w działaniu.

Przechowywanie poświadczeń zabezpieczone w konfiguracji węzła (pliku MOF konfiguracji dokumenty) wymaga szyfrowania poświadczeń w pliku MOF konfiguracji węzła. Jednak obecnie musisz poinformować DSC programu PowerShell nie jest akceptowalne o poświadczenia, które mają zostać zwrócone w postaci zwykłego tekstu podczas generowania pliku MOF konfiguracji węzła, ponieważ DSC programu PowerShell nie wie, że usługi Azure Automation będzie można szyfrowanie całego pliku MOF po jej generacji za pomocą zadania kompilacji.

Można stwierdzić, DSC programu PowerShell, który ma nic złego o poświadczenia, które mają zostać zwrócone w postaci zwykłego tekstu w konfiguracji węzła wygenerowane pliki MOF przy użyciu [ **ConfigurationData**](#configurationdata). Należy przekazać `PSDscAllowPlainTextPassword = $true` za pośrednictwem **ConfigurationData** dla każdego bloku węzła, którego nazwa jest wyświetlana w konfiguracji DSC i przy użyciu poświadczeń.

Konfiguracja DSC, która używa zasób poświadczenia usługi Automation można znaleźć w poniższym przykładzie.

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

Można kompilować tej konfiguracji DSC przy użyciu programu PowerShell. Następujące polecenie programu PowerShell dodaje dwie konfiguracje węzłów do serwera usługi Azure Automation stan konfiguracji ściągania: **CredentialSample.MyVM1** i **CredentialSample.MyVM2**.

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
> Po zakończeniu kompilacji może zostać wyświetlony błąd wskazujący: **Nie można zaimportować modułu "Microsoft.PowerShell.Management", ponieważ przystawka "Microsoft.PowerShell.Management" została już zaimportowana.** Można bezpiecznie zignorować to ostrzeżenie.

## <a name="partial-configuration"></a>Częściowe konfiguracji

Konfiguracja stanu usługi Azure Automation obsługuje użycie [konfiguracje częściowe](https://docs.microsoft.com/powershell/dsc/pull-server/partialconfigs).
W tym scenariuszu skonfigurowano niezależnie zarządzać wieloma konfiguracjami DSC, a każda z konfiguracji jest retreieved z usługi Azure Automation.
Jednak tylko jedną konfigurację można przypisać do węzła na konto usługi automation.
Oznacza to, jeśli używane są dwie konfiguracje dla węzła będzie wymagać dwóch kont usługi automation.
Aby dowiedzieć się więcej o tym, jak zespoły mogą współdziałać razem wspólnie zarządzać serwerami przy użyciu konfiguracji jako kodu zobacz [DSC opis roli w potoku CI/CD](https://docs.microsoft.com/powershell/dsc/overview/authoringadvanced).

## <a name="importing-node-configurations"></a>Importowanie konfiguracji węzła

Można również zaimportować konfiguracje węzłów (pliki MOF), które zostały skompilowane spoza platformy Azure. Jedną z zalet to jest, aby były podpisane konfiguracje węzłów. Konfiguracji węzła podpisem jest weryfikowany lokalnie w węźle zarządzanym przez agenta DSC, zapewniając, że konfiguracja stosowana do węzła pochodzi z autoryzowanego źródła.

> [!NOTE]
> Umożliwia importowanie podpisane konfiguracje na koncie usługi Azure Automation, ale usługa Azure Automation nie obsługuje obecnie kompilowanie podpisanej konfiguracji.

> [!NOTE]
> Plik konfiguracji węzła nie może być większa niż 1 MB, aby zezwalała na można zaimportować do usługi Azure Automation.

Aby uzyskać więcej informacji na temat metod podpisywania konfiguracje węzłów, zobacz [poprawę WMF 5.1 — instrukcje konfiguracji logowania i moduł](/powershell/wmf/5.1/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importowanie konfiguracji węzła w witrynie Azure portal

1. Na koncie usługi Automation kliknij **State configuration (DSC)** w obszarze **zarządzania konfiguracją**.
1. W **State configuration (DSC)** kliknij na **konfiguracje** , a następnie kliknij **+ Dodaj**.
1. W **importu** stronie, kliknij ikonę folderu **plik konfiguracji węzła** pole tekstowe, aby przejść do pliku konfiguracji węzła (MOF) na komputerze lokalnym.

   ![Przeglądaj w poszukiwaniu pliku lokalnego](./media/automation-dsc-compile/import-browse.png)

1. Wprowadź nazwę w **Nazwa konfiguracji** pola tekstowego. Ta nazwa musi odpowiadać Nazwa konfiguracji, z którego został skompilowany konfiguracji węzła.
1. Kliknij przycisk **OK**.

### <a name="importing-a-node-configuration-with-powershell"></a>Importowanie konfiguracji węzła przy użyciu programu PowerShell

Możesz użyć [AzureRmAutomationDscNodeConfiguration importu](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) polecenia cmdlet, aby zaimportować konfigurację węzła do konta usługi automation.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć pracę, zobacz [wprowadzenie do usługi Azure Automation stanu konfiguracji](automation-dsc-getting-started.md)
- Aby dowiedzieć się więcej na temat kompilowanie konfiguracji DSC, dzięki czemu można je przypisać do węzłów docelowych, zobacz [kompilowanie konfiguracji w konfiguracji stan automatyzacji platformy Azure](automation-dsc-compile.md)
- Dokumentacja poleceń cmdlet programu PowerShell, można zobaczyć [poleceń cmdlet usługi Azure Automation stanu konfiguracji](/powershell/module/azurerm.automation/#automation)
- Aby uzyskać informacje o cenach, zobacz [cennika usługi Azure Automation stanu konfiguracji](https://azure.microsoft.com/pricing/details/automation/)
- Aby wyświetlić przykład użycia usługi Azure Automation stanu konfiguracji w potoku ciągłego wdrażania, zobacz [ciągłe przy użyciu usługi Azure Automation stan konfiguracji wdrożenia i narzędzia Chocolatey](automation-dsc-cd-chocolatey.md)
