---
title: Włącz diagnostykę w usług Azure Cloud Services przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć diagnostyki usługi w chmurze przy użyciu programu PowerShell
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: 66e08754-8639-4022-ae18-4237749ba17d
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: adegeo
ms.openlocfilehash: 8dd9724981860c9cd4ccc443cc2bfdc465811e7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843427"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Włącz diagnostykę w usług Azure Cloud Services przy użyciu programu PowerShell
Można zebrać danych diagnostycznych, takich jak dzienniki aplikacji z usługą w chmurze przy użyciu rozszerzenia diagnostyki Azure itp. liczniki wydajności. W tym artykule opisano sposób włączania rozszerzenia diagnostyki Azure dla usługi w chmurze przy użyciu programu PowerShell.  Zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) wymagań wstępnych wymagane dla tego artykułu.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Włączanie rozszerzenia diagnostyki w ramach wdrażania usługi Cloud Service
Takie podejście stosuje się do typu ciągłej integracji scenariuszy, w którym można włączyć rozszerzenia diagnostyki w ramach wdrażania usługi w chmurze. Podczas tworzenia nowego wdrożenia usługi w chmurze można włączyć rozszerzenia diagnostyki przez przekazywanie *ExtensionConfiguration* parametr [AzureDeployment nowy](/powershell/module/azure/new-azuredeployment?view=azuresmps-3.7.0) polecenia cmdlet. *ExtensionConfiguration* parametr pobiera tablicę diagnostyki konfiguracje, które mogą być tworzone przy użyciu [AzureServiceDiagnosticsExtensionConfig nowy](/powershell/module/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) polecenia cmdlet.

W poniższym przykładzie pokazano, jak można włączyć diagnostyki sieć Web i proces roboczy, o konfiguracji diagnostyki innej usługi w chmurze.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Jeśli plik konfiguracji diagnostyki Określa `StorageAccount` element nazwy konta magazynu, a następnie `New-AzureServiceDiagnosticsExtensionConfig` polecenia cmdlet będą automatycznie używać tego konta magazynu. Aby to zrobić konta magazynu musi być w tej samej subskrypcji co wdrażania usługi w chmurze.

Z Azure SDK 2.6 ponownego udostępnienia plików konfiguracji rozszerzeń, które są generowane przez program MSBuild publikowanie danych wyjściowych będzie zawierać nazwy konta magazynu, które są oparte na ciąg konfiguracji diagnostyki określony w pliku konfiguracji usługi (cscfg). Poniższy skrypt pokazuje, jak przeanalizować pliki konfiguracji rozszerzenia z danych wyjściowych publikowania i skonfigurować rozszerzenia diagnostyki dla każdej roli, podczas wdrażania usługi w chmurze.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online używa podejście podobne zautomatyzowanych wdrożeń usług w chmurze z rozszerzeniem diagnostyki. Zobacz [AzureCloudDeployment.ps1 publikowania](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) pełny przykład.

Jeśli nie `StorageAccount` został określony w konfiguracji diagnostyki muszą podawać *StorageAccountName* parametr w poleceniu cmdlet. Jeśli *StorageAccountName* określony parametr, a następnie polecenia cmdlet będzie zawsze używać konta magazynu, które jest określone w parametrze i nie ten, który jest określony w pliku konfiguracji diagnostyki.

Jeśli konto magazynu diagnostyki jest w innej subskrypcji z usługi w chmurze, a następnie jawnie przekazywane w *StorageAccountName* i *StorageAccountKey* parametry polecenia cmdlet. *StorageAccountKey* parametru nie jest wymagana, gdy konto magazynu diagnostyki jest w tej samej subskrypcji, ponieważ polecenie cmdlet można automatycznie zapytania i ustaw wartość klucza podczas włączania rozszerzenia diagnostyki. Jednak jeśli konto magazynu diagnostyki jest w innej subskrypcji, to polecenie cmdlet nie może być może automatycznie pobrać klucz i musisz jawnie określić klucz przy użyciu *StorageAccountKey* parametru.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Włączanie rozszerzenia diagnostyki w istniejącej usłudze Cloud Service
Można użyć [AzureServiceDiagnosticsExtension zestaw](/powershell/module/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) polecenia cmdlet, aby włączyć lub zaktualizować diagnostyki konfiguracji usługi w chmurze, która jest już uruchomiona.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Pobieranie bieżącej konfiguracji rozszerzenia diagnostyki
Użyj [Get-AzureServiceDiagnosticsExtension](/powershell/module/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) polecenia cmdlet, aby uzyskać bieżącą konfigurację diagnostyki w usłudze w chmurze.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Usuwanie rozszerzenia diagnostyki
Aby wyłączyć funkcję diagnostyki na usługi w chmurze można użyć [AzureServiceDiagnosticsExtension Usuń](/powershell/module/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) polecenia cmdlet.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Jeśli włączono rozszerzenie diagnostyki za pomocą *AzureServiceDiagnosticsExtension zestaw* lub *AzureServiceDiagnosticsExtensionConfig nowy* bez *roli*parametr, a następnie można usunąć przy użyciu rozszerzenia *AzureServiceDiagnosticsExtension Usuń* bez *roli* parametru. Jeśli *roli* parametr został użyty podczas włączania rozszerzenia, a następnie go należy użyć podczas usuwania rozszerzenia.

Aby usunąć rozszerzenie diagnostyki z pojedynczej roli:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać dodatkowe wskazówki na temat używania diagnostyki Azure i innych technik rozwiązywania problemów, zobacz [Włączanie diagnostyki w usług Azure Cloud Services i maszyn wirtualnych](cloud-services-dotnet-diagnostics.md).
* [Schemat konfiguracji diagnostyki](https://msdn.microsoft.com/library/azure/dn782207.aspx) opisano różne opcje konfiguracji xml rozszerzenia diagnostyki.
* Aby dowiedzieć się, jak włączyć rozszerzenie diagnostyki dla maszyn wirtualnych, zobacz [Utwórz maszynę wirtualną systemu Windows z monitorowania i diagnostyki za pomocą szablonu usługi Resource Manager Azure](../virtual-machines/windows/extensions-diagnostics-template.md)
