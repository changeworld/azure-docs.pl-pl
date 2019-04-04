---
title: Włączanie diagnostyki w usługach Azure Cloud Services przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć diagnostykę usług w chmurze przy użyciu programu PowerShell
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 66e08754-8639-4022-ae18-4237749ba17d
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: jeconnoc
ms.openlocfilehash: b20fa7a1f43369cde85c2535637eec7ceb1d3c29
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918335"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Włączanie diagnostyki w usługach Azure Cloud Services przy użyciu programu PowerShell
Można zbierać dane diagnostyczne, takie jak dzienniki aplikacji, liczniki wydajności, itp. z usługi w chmurze przy użyciu rozszerzenia diagnostyki platformy Azure. W tym artykule opisano, jak włączyć rozszerzenie diagnostyki platformy Azure dla usługi w chmurze przy użyciu programu PowerShell.  Zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview) wymagań wstępnych wymagane na potrzeby tego artykułu.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Włączanie rozszerzenia diagnostyki w ramach wdrażania usługi Cloud Service
To podejście jest odpowiednie do ciągłej integracji typu scenariuszy, w którym można włączyć rozszerzenia diagnostyki w ramach wdrażania usługi w chmurze. Podczas tworzenia nowego wdrożenia usługi w chmurze można włączyć rozszerzenie diagnostyki, przekazując *ExtensionConfiguration* parametr [New AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) polecenia cmdlet. *ExtensionConfiguration* Parametr przyjmuje tablicę konfiguracji diagnostycznych, które mogą być tworzone przy użyciu [New AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) polecenia cmdlet.

Poniższy przykład pokazuje, jak włączyć diagnostyki usługi w chmurze za pomocą WebRole i workerrole —, posiadające konfiguraci diagnostiky różne.

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

Jeśli plik konfiguracji diagnostyki nie określa `StorageAccount` element nazwą konta magazynu, a następnie `New-AzureServiceDiagnosticsExtensionConfig` polecenie cmdlet będzie automatycznie używać tego konta magazynu. Aby to działało konto magazynu musi znajdować się w tej samej subskrypcji co usługa w chmurze są wdrożone.

Z Azure SDK 2.6 są publikowania plików konfiguracji rozszerzeń, które są generowane przez program MSBuild docelowy będzie zawierać nazwę konta magazynu, na podstawie parametrów konfiguracji diagnostyki, które są określone w pliku konfiguracji usługi (cscfg). Poniższy skrypt pokazuje, jak analizować pliki konfiguracji rozszerzenia z docelowym publikowanych danych wyjściowych i skonfigurować rozszerzenie diagnostyki dla każdej roli, podczas wdrażania usługi w chmurze.

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

Visual Studio Online korzysta z podobnego podejścia używanie wdrożeń zautomatyzowanych z usług w chmurze za pomocą rozszerzenia diagnostyki. Zobacz [AzureCloudDeployment.ps1 Publikuj](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) pełny przykład.

Jeśli nie `StorageAccount` została określona w konfiguracji diagnostyki, wówczas należy przekazać *StorageAccountName* parametru do polecenia cmdlet. Jeśli *StorageAccountName* parametr jest określony, a następnie polecenie cmdlet zawsze będzie korzystać z konta magazynu, który jest określony w parametrze i nie ten, który jest określony w pliku konfiguracji diagnostyki.

Jeśli konto magazynu diagnostyki jest w innej subskrypcji z usługi w chmurze, a następnie musisz przekazać jawnie *StorageAccountName* i *StorageAccountKey* parametry do polecenia cmdlet. *StorageAccountKey* parametr nie jest potrzebny, gdy konto magazynu diagnostyki jest w tej samej subskrypcji, jak polecenie cmdlet automatycznie wykonywać zapytania i ustaw wartość klucza podczas włączania rozszerzenia diagnostyki. Jeśli jednak konto magazynu diagnostyki w innej subskrypcji, to polecenie cmdlet może nie móc automatycznie uzyskać klucz i należy jawnie określić klucz przy użyciu *StorageAccountKey* parametru.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Włączanie rozszerzenia diagnostyki w istniejącej usłudze Cloud Service
Możesz użyć [AzureServiceDiagnosticsExtension zestaw](/powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) polecenia cmdlet, aby włączyć lub zaktualizuj konfigurację diagnostyki w usłudze w chmurze, która jest już uruchomiona.

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
Użyj [Get AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) polecenia cmdlet, aby uzyskać bieżącą konfigurację diagnostyki usługi w chmurze.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Usuwanie rozszerzenia diagnostyki
Aby wyłączyć diagnostykę w usłudze w chmurze można użyć [AzureServiceDiagnosticsExtension Usuń](/powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) polecenia cmdlet.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Jeśli włączono rozszerzenie diagnostyki za pomocą *AzureServiceDiagnosticsExtension zestaw* lub *New AzureServiceDiagnosticsExtensionConfig* bez *roli*parametr, a następnie można usunąć rozszerzenie przy użyciu polecenia *AzureServiceDiagnosticsExtension Usuń* bez *roli* parametru. Jeśli *roli* użyto parametru podczas włączania rozszerzenia, a następnie go musi też być używane podczas usuwania rozszerzenia.

Aby usunąć rozszerzenie diagnostyki z pojedynczej roli:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać dodatkowe wskazówki na temat korzystania z diagnostyki platformy Azure i innych technik rozwiązywania problemów, zobacz [Włączanie diagnostyki w usługach Azure Cloud Services i Virtual Machines](cloud-services-dotnet-diagnostics.md).
* [Schemat usługi Diagnostyka konfiguracji](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot2) opisano różne opcje konfiguracji rozszerzenia diagnostyki xml.
* Aby dowiedzieć się, jak włączyć rozszerzenie diagnostyki dla maszyn wirtualnych, zobacz [Utwórz maszynę wirtualną Windows z funkcjami monitorowania i diagnostyki przy użyciu szablonu Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md)
