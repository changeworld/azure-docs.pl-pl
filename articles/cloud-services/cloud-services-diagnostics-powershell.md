---
title: Włącz diagnostykę w usługach w chmurze azure przy użyciu programu PowerShell | Dokumenty firmy Microsoft
description: Dowiedz się, jak włączyć diagnostykę usług w chmurze przy użyciu programu PowerShell
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: tagore
ms.openlocfilehash: 76cdffed813fd182980b36f848e0ae42f3226539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386548"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Włączanie diagnostyki w usługach w chmurze azure przy użyciu programu PowerShell
Można zbierać dane diagnostyczne, takie jak dzienniki aplikacji, liczniki wydajności itp. W tym artykule opisano sposób włączania rozszerzenia diagnostyki platformy Azure dla usługi w chmurze przy użyciu programu PowerShell.  Zobacz [Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview) dla wymagań wstępnych wymaganych dla tego artykułu.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Włączanie rozszerzenia diagnostyki w ramach wdrażania usługi Cloud Service
Takie podejście ma zastosowanie do ciągłej integracji typu scenariuszy, gdzie rozszerzenie diagnostyki można włączyć w ramach wdrażania usługi w chmurze. Podczas tworzenia nowego wdrożenia usługi w chmurze, można włączyć rozszerzenie diagnostyki, przekazując w *ExtensionConfiguration* parametr do polecenia cmdlet [New-AzureDeployment.](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) *ExtensionConfiguration* Parametr przyjmuje tablicę konfiguracji diagnostycznych, które mogą być tworzone przy użyciu polecenia cmdlet [New-AzureServiceDiagnosticsExtensionConfig.](/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0)

W poniższym przykładzie pokazano, jak można włączyć diagnostykę usługi w chmurze za pomocą webrole i WorkerRole, każdy o innej konfiguracji diagnostyki.

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

Jeśli plik konfiguracji diagnostyki `StorageAccount` określa element o nazwie konta `New-AzureServiceDiagnosticsExtensionConfig` magazynu, polecenie cmdlet automatycznie użyje tego konta magazynu. Aby to działało, konto magazynu musi znajdować się w tej samej subskrypcji, co wdrażana usługa w chmurze.

Począwszy od usługi Azure SDK 2.6 pliki konfiguracji rozszerzenia generowane przez dane wyjściowe docelowe publikowania msBuild będą zawierać nazwę konta magazynu na podstawie ciągu konfiguracji diagnostyki określonego w pliku konfiguracji usługi (cscfg). Poniższy skrypt pokazuje, jak analizować pliki konfiguracji rozszerzenia z danych wyjściowych publikowania docelowych i skonfigurować rozszerzenie diagnostyki dla każdej roli podczas wdrażania usługi w chmurze.

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

Visual Studio Online używa podobnego podejścia do automatycznego wdrażania usług w chmurze z rozszerzeniem diagnostyki. Zobacz [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) dla pełnego przykładu.

Jeśli `StorageAccount` w konfiguracji diagnostyki nie określono żadnego, należy przekazać parametr *StorageAccountName* do polecenia cmdlet. Jeśli *parametr StorageAccountName* jest określony, polecenie cmdlet zawsze będzie używać konta magazynu określonego w parametrze, a nie tego, które jest określone w pliku konfiguracji diagnostyki.

Jeśli konto magazynu diagnostyki znajduje się w innej subskrypcji niż usługa w chmurze, należy jawnie przekazać w *StorageAccountName* i *StorageAccountKey* parametry do polecenia cmdlet. *Parametr StorageAccountKey* nie jest potrzebny, gdy konto magazynu diagnostyki znajduje się w tej samej subskrypcji, ponieważ polecenie cmdlet może automatycznie wysyłać zapytania i ustawiać wartość klucza podczas włączania rozszerzenia diagnostyki. Jednak jeśli konto magazynu diagnostyki jest w innej subskrypcji, a następnie polecenie cmdlet może nie być w stanie uzyskać klucz automatycznie i trzeba jawnie określić klucz za pośrednictwem *StorageAccountKey* parametru.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Włączanie rozszerzenia diagnostyki w istniejącej usłudze Cloud Service
Polecenia cmdlet [Set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) można użyć, aby włączyć lub zaktualizować konfigurację diagnostyki w usłudze w chmurze, która jest już uruchomiona.

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
Użyj polecenia cmdlet [Get-AzureServiceDiagnosticsExtension,](/powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) aby uzyskać bieżącą konfigurację diagnostyki usługi w chmurze.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Usuwanie rozszerzenia diagnostyki
Aby wyłączyć diagnostykę w usłudze w chmurze, można użyć polecenia cmdlet [Remove-AzureServiceDiagnosticsExtension.](/powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0)

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Jeśli włączono rozszerzenie diagnostyki przy użyciu *funkcji Set-AzureServiceDiagnosticsExtension* lub *New-AzureServiceDiagnosticsExtensionConfig* bez parametru *Role,* można usunąć rozszerzenie przy użyciu *funkcji Usuń-AzureServiceDiagnosticsExtension* bez parametru *Role.* Jeśli *funkcja parametru* został użyty podczas włączania rozszerzenia, a następnie musi być również używany podczas usuwania rozszerzenia.

Aby usunąć rozszerzenie diagnostyki z pojedynczej roli:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać dodatkowe wskazówki dotyczące korzystania z diagnostyki platformy Azure i innych technik rozwiązywania problemów, zobacz [Włączanie diagnostyki w usługach w chmurze Azure i maszynach wirtualnych.](cloud-services-dotnet-diagnostics.md)
* [Schemat konfiguracji diagnostyki](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot3) wyjaśnia różne opcje konfiguracji xml dla rozszerzenia diagnostyki.
* Aby dowiedzieć się, jak włączyć rozszerzenie diagnostyki dla maszyn wirtualnych, zobacz [Tworzenie maszyny wirtualnej systemu Windows z monitorowaniem i diagnostyką przy użyciu szablonu usługi Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md)



