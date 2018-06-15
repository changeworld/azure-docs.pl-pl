---
title: Rozszerzenia maszyn wirtualnych i funkcje systemu Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jakie rozszerzenia są dostępne dla maszyn wirtualnych platformy Azure, pogrupowane według co ich Podaj puli zasobów i zwiększyć.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/06/2017
ms.author: danis
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 09eb2e723be80fd623ad53c9bda8271ef8846a0f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192438"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Rozszerzenia maszyn wirtualnych i funkcje systemu Windows

Rozszerzenia maszyny wirtualnej platformy Azure są małe aplikacji, które mają po wdrożeniu i automatyzację zadań na maszynach wirtualnych Azure. Na przykład jeśli maszyna wirtualna wymaga instalacji oprogramowania, ochrony oprogramowania antywirusowego lub Docker konfiguracji, rozszerzenia maszyny Wirtualnej może służyć do wykonania tych zadań. Rozszerzenia maszyny Wirtualnej platformy Azure można uruchomić przy użyciu wiersza polecenia platformy Azure, programu PowerShell, szablonów usługi Azure Resource Manager i portalu Azure. Rozszerzenia mogą powiązany z nowego wdrożenia maszyny wirtualnej lub uruchomienia dowolnego istniejącego systemu.

Ten dokument zawiera omówienie rozszerzeń maszyny wirtualnej, wymagań wstępnych dotyczących używania rozszerzenia maszyny wirtualnej i wskazówki dotyczące sposobu wykrywania, zarządzanie i usuwanie rozszerzenia maszyny wirtualnej. Ten dokument zawiera informacje uogólniony wielu rozszerzeń maszyny Wirtualnej nie są dostępne, każde z nich potencjalnie unikatowych konfiguracji. Szczegółów dotyczących poszczególnych rozszerzeń można znaleźć w każdy dokument specyficzne dla poszczególnych rozszerzenia.

## <a name="use-cases-and-samples"></a>Przypadki użycia i przykłady

Istnieje wiele różnych rozszerzeń maszyny Wirtualnej platformy Azure, każde z nich określony przypadek użycia. Niektóre przykładowe przypadki użycia są:

- Dotyczą konfiguracji środowiska PowerShell żądany stan maszyny wirtualnej za pomocą rozszerzenia usługi Konfiguracja DSC dla systemu Windows. Aby uzyskać więcej informacji, zobacz [Azure żądany stan konfiguracji rozszerzenia](extensions-dsc-overview.md).
- Skonfiguruj monitorowanie maszyny wirtualnej przy użyciu rozszerzenia maszyny Wirtualnej agenta monitorowania firmy Microsoft. Aby uzyskać więcej informacji, zobacz [łączenie maszyn wirtualnych platformy Azure do analizy dzienników](../../log-analytics/log-analytics-azure-vm-extension.md).
- Skonfiguruj Monitorowanie infrastruktury platformy Azure z rozszerzeniem Datadog. Aby uzyskać więcej informacji, zobacz [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Skonfiguruj maszynę wirtualną platformy Azure przy użyciu Chef. Aby uzyskać więcej informacji, zobacz [automatyzacji Azure wdrożenia maszyny wirtualnej z Chef](chef-automation.md).

Oprócz rozszerzenia procesu rozszerzenia niestandardowego skryptu jest dostępna dla maszyn wirtualnych w systemach Windows i Linux. Rozszerzenia niestandardowego skryptu dla systemu Windows umożliwia dowolny skrypt programu PowerShell do uruchamiania na maszynie wirtualnej. Jest to przydatne podczas projektowania wdrożeń platformy Azure, które wymagają konfiguracji oprócz zapewniają jakie natywnego narzędzia Azure. Aby uzyskać więcej informacji, zobacz [rozszerzenie skryptu niestandardowego maszyny Wirtualnej systemu Windows](extensions-customscript.md).


## <a name="prerequisites"></a>Wymagania wstępne

Każde rozszerzenie maszyny wirtualnej może mieć własny zestaw wymagań wstępnych. Dla wystąpienia rozszerzenia maszyny Wirtualnej platformy Docker jest wymaganiem wstępnym obsługiwanych dystrybucji systemu Linux. Wymagania dotyczące poszczególnych rozszerzeń są szczegółowo opisane w dokumentacji specyficzne dla rozszerzenia.

### <a name="azure-vm-agent"></a>Agent maszyny wirtualnej platformy Azure
Agent maszyny Wirtualnej platformy Azure zarządza interakcji między maszyny wirtualnej platformy Azure i kontroler sieci szkieletowej Azure. Agent maszyny Wirtualnej jest odpowiedzialny za dużo aspekty funkcjonalne, wdrażania i zarządzania maszynami wirtualnymi platformy Azure, łącznie z rozszerzeniami maszyny Wirtualnej. Agent maszyny Wirtualnej platformy Azure jest preinstalowany na portalu Azure Marketplace obrazów i można zainstalować w obsługiwanych systemach operacyjnych.

Aby uzyskać informacje dotyczące obsługiwanych systemów operacyjnych i instrukcje dotyczące instalacji, zobacz [agenta maszyny wirtualnej platformy Azure](agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Odnajdywanie rozszerzeń maszyny Wirtualnej
Wiele różnych rozszerzeń maszyny Wirtualnej są dostępne do użycia z maszyn wirtualnych platformy Azure. Aby wyświetlić pełną listę, uruchom następujące polecenie z modułu programu PowerShell usługi Azure Resource Manager. Upewnij się, że określ żądaną lokalizację po uruchomieniu tego polecenia.

```powershell
Get-AzureRmVmImagePublisher -Location WestUS | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Uruchom rozszerzeń maszyny Wirtualnej

Rozszerzenia maszyny wirtualnej platformy Azure może działać w istniejących maszyn wirtualnych, co jest przydatne, gdy trzeba wprowadzić zmiany w konfiguracji lub Przywróć łączność w przypadku wdrożonej maszyny Wirtualnej. Rozszerzenia maszyny Wirtualnej dołączany także wdrażanie szablonów usługi Azure Resource Manager. Za pomocą rozszerzeń z szablonami usługi Resource Manager, można włączyć maszyn wirtualnych platformy Azure wdrożyć i skonfigurować bez konieczności interwencji po wdrożeniu.

Następujących metod można uruchomić rozszerzenia dla istniejącej maszyny wirtualnej.

### <a name="powershell"></a>PowerShell

Istnieje kilka poleceń programu PowerShell do uruchamiania poszczególnych rozszerzeń. Umożliwia wyświetlenie listy, uruchom następujące polecenia programu PowerShell.

```powershell
get-command Set-AzureRM*Extension* -Module AzureRM.Compute
```

Zapewnia to dane wyjściowe podobne do następującego:

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    2.2.0      AzureRM.Compute
```

W poniższym przykładzie użyto rozszerzenia niestandardowego skryptu do pobrania skryptu z repozytorium GitHub na docelowej maszynie wirtualnej, a następnie uruchom skrypt. Aby uzyskać więcej informacji dotyczących rozszerzenia niestandardowego skryptu, zobacz [Omówienie rozszerzenia niestandardowego skryptu](extensions-customscript.md).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

W tym przykładzie rozszerzenia dostępu do maszyny Wirtualnej służy do resetowania hasła administracyjnego maszyny wirtualnej systemu Windows. Aby uzyskać więcej informacji dotyczących rozszerzenia dostępu do maszyny Wirtualnej, zobacz [usługi pulpitu zdalnego resetowania na maszynie wirtualnej Windows](reset-rdp.md).

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

`Set-AzureRmVMExtension` Polecenia można uruchomić wszystkie rozszerzenia maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [odwołania zestawu AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx).


### <a name="azure-portal"></a>Azure Portal

Rozszerzenia maszyny Wirtualnej może odnosić się do istniejącej maszyny wirtualnej za pośrednictwem portalu Azure. Aby to zrobić, wybierz maszynę wirtualną, aby użyć, wybierz **rozszerzenia**i kliknij przycisk **Dodaj**. Zapewnia to listę dostępnych rozszerzeń. Wybierz jedną, mają i postępuj zgodnie z instrukcjami kreatora.

Na poniższej ilustracji przedstawiono instalacja rozszerzenia Microsoft Antimalware z portalu Azure.

![Zainstaluj rozszerzenie ochrony przed złośliwym oprogramowaniem](./media/extensions-features/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Rozszerzenia maszyny Wirtualnej można dodać do szablonu usługi Azure Resource Manager i wykonywane przy użyciu wdrażania szablonu. Wdrażanie rozszerzeń przy użyciu szablonu jest przydatne w przypadku tworzenia wdrożeń platformy Azure w pełni skonfigurowany. Na przykład następujący kod JSON jest pobierana z szablonem usługi Resource Manager, który wdraża zestaw maszyn wirtualnych z równoważeniem obciążenia i bazy danych Azure SQL, a następnie instaluje aplikację .NET Core na każdej maszynie Wirtualnej. Rozszerzenia maszyny Wirtualnej zapewnia obsługę instalacji oprogramowania.

Aby uzyskać więcej informacji, zobacz [pełne szablonu usługi Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Aby uzyskać więcej informacji, zobacz [szablony Authoring Azure Resource Manager z rozszerzeniami maszyny Wirtualnej systemu Windows](template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Zabezpieczanie danych rozszerzenia maszyny Wirtualnej

Jeśli korzystasz z rozszerzenia maszyny Wirtualnej, może być konieczne jest stosowanie poufne informacje, takie jak poświadczeń, nazwy konta magazynu i klucze dostępu do konta magazynu. Wiele rozszerzeń maszyny Wirtualnej obejmują konfiguracji chronionych danych i tylko odszyfrowującego wewnątrz docelowej maszyny wirtualnej. Każde rozszerzenie ma używającym w dokumentacji konkretnego rozszerzenia schematu określonej konfiguracji chronionych.

W poniższym przykładzie przedstawiono wystąpienia rozszerzenia niestandardowego skryptu dla systemu Windows. Zwróć uwagę, że polecenie do wykonania zawiera zestaw poświadczeń. W tym przykładzie wykonanie polecenia nie będą szyfrowane.


```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Bezpieczny ciąg wykonywania przez przeniesienie **polecenie do wykonania** właściwości **chronione** konfiguracji.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

## <a name="troubleshoot-vm-extensions"></a>Rozwiązywanie problemów z rozszerzeń maszyny Wirtualnej

Każde rozszerzenie maszyny Wirtualnej może być określone kroki rozwiązywania problemów. Na przykład podczas korzystania z rozszerzenia niestandardowego skryptu, szczegóły wykonywania skryptu można znaleźć lokalnie na maszynie wirtualnej, na którym uruchomiono rozszerzenia. Wszystkie kroki rozwiązywania problemów specyficzne dla rozszerzenia są szczegółowo opisane w dokumentacji specyficzne dla rozszerzenia.

Następujące kroki dotyczą wszystkich rozszerzeń maszyny wirtualnej.

### <a name="view-extension-status"></a>Wyświetl stan rozszerzenia

Po uruchomieniu rozszerzenie maszyny wirtualnej na maszynie wirtualnej, użyj następującego polecenia programu PowerShell, aby zwrócić stan rozszerzenia. Zastąp przykładowe nazwy parametrów własne wartości. `Name` Parametr przyjmuje Nazwa rozszerzenia w czasie wykonywania.

```PowerShell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Dane wyjściowe wygląda następująco:

```json
ResourceGroupName       : myResourceGroup
VMName                  : myVM
Name                    : myExtensionName
Location                : westus
Etag                    : null
Publisher               : Microsoft.Azure.Extensions
ExtensionType           : DockerExtension
TypeHandlerVersion      : 1.0
Id                      : /subscriptions/mySubscriptionIS/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/myExtensionName
PublicSettings          :
ProtectedSettings       :
ProvisioningState       : Succeeded
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : False
ForceUpdateTag          :
```

Stan wykonania rozszerzenia można znaleźć w portalu Azure. Aby wyświetlić stan rozszerzenia, wybierz maszynę wirtualną, wybierz polecenie **rozszerzenia**i wybierz żądane rozszerzenie.

### <a name="rerun-vm-extensions"></a>Uruchom ponownie rozszerzeń maszyny Wirtualnej

Może to być przypadkach, w których można ponownie uruchomić wymaga rozszerzenia maszyny wirtualnej. Można to zrobić, usuwając rozszerzenie, a następnie ponowne uruchomienie rozszerzenia metodą wykonywania wybranych przez użytkownika. Aby usunąć rozszerzenie, uruchom następujące polecenie z modułu Azure PowerShell. Zastąp przykładowe nazwy parametrów własne wartości.

```powershell
Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Rozszerzenie może zostać także usunięty przy użyciu portalu Azure. W tym celu:

1. Wybierz maszynę wirtualną.
2. Wybierz **rozszerzenia**.
3. Wybierz żądane rozszerzenie.
4. Wybierz **odinstalować**.

## <a name="common-vm-extensions-reference"></a>Typowe odwołanie do rozszerzenia maszyny Wirtualnej
| Nazwa rozszerzenia | Opis | Więcej informacji |
| --- | --- | --- |
| Niestandardowe rozszerzenie skryptu dla systemu Windows |Uruchom skrypty przed maszyny wirtualnej platformy Azure |[Niestandardowe rozszerzenie skryptu dla systemu Windows](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Rozszerzenia konfiguracji DSC dla systemu Windows |Rozszerzenia DSC (konfiguracji żądanego stanu) programu PowerShell |[Rozszerzenia konfiguracji DSC dla systemu Windows](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Rozszerzenie Diagnostyki Azure |Zarządzanie Diagnostyka Azure |[Rozszerzenie diagnostyki platformy Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozszerzenia dostępu do maszyny Wirtualnej platformy Azure |Zarządzaj użytkownikami i poświadczenia |[Rozszerzenia dostępu do maszyny Wirtualnej dla systemu Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
