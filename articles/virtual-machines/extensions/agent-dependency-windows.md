---
title: Rozszerzenie maszyny wirtualnej zależności usługi Azure Monitor dla systemu Windows
description: Wdrażanie agenta zależności usługi Azure Monitor na maszynie wirtualnej systemu Windows przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 27d43af2d5860d287d8b5914379747ae528db34b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250675"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Rozszerzenie maszyny wirtualnej zależności usługi Azure Monitor dla systemu Windows

Funkcja mapa usługi Azure Monitor dla maszyn wirtualnych pobiera swoje dane z agenta zależności firmy Microsoft. Rozszerzenie maszyny wirtualnej agenta zależności platformy Azure dla systemu Windows jest publikowane i obsługiwane przez firmę Microsoft. Rozszerzenie instaluje agenta zależności na maszynach wirtualnych platformy Azure. Ten dokument zawiera szczegółowe informacje na temat obsługiwanych platform, konfiguracji i opcji wdrażania rozszerzenia maszyny wirtualnej agenta zależności maszyny platformy Azure dla systemu Windows.

## <a name="operating-system"></a>System operacyjny

Rozszerzenie agenta zależności maszyny wirtualnej platformy Azure dla systemu Windows można uruchomić na podstawie obsługiwanych systemów operacyjnych wymienionych w sekcji [Obsługiwane systemy operacyjne](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) usługi Azure Monitor dla maszyn wirtualnych wdrożenia artykułu.

## <a name="extension-schema"></a>Schemat rozszerzenia

W poniższym programie JSON przedstawiono schemat rozszerzenia agenta zależności maszyny Wirtualnej platformy Azure na maszynie wirtualnej systemu Windows platformy Azure.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość/przykład |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| wydawca | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentWindows |
| typHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonów usługi Azure Resource Manager. Schemat JSON opisany w poprzedniej sekcji w szablonie usługi Azure Resource Manager umożliwia uruchomienie rozszerzenia agenta zależności maszyny Wirtualnej platformy Azure podczas wdrażania szablonu usługi Azure Resource Manager.

JSON dla rozszerzenia maszyny wirtualnej może być zagnieżdżone wewnątrz zasobu maszyny wirtualnej. Można też umieścić go na głównym lub najwyższym poziomie szablonu JSON Menedżera zasobów. Położenie JSON wpływa na wartość nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustawianie nazwy i typu zasobów podrzędnych](../../azure-resource-manager/templates/child-resource-name-type.md).

W poniższym przykładzie przyjęto założenie, że rozszerzenie agenta zależności jest zagnieżdżone wewnątrz zasobu maszyny wirtualnej. Podczas zagnieżdżania zasobu rozszerzenia, `"resources": []` JSON jest umieszczany w obiekcie maszyny wirtualnej.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Po umieszczeniu rozszerzenia JSON w katalogu głównym szablonu nazwa zasobu zawiera odwołanie do nadrzędnej maszyny wirtualnej. Typ odzwierciedla konfigurację zagnieżdżoną.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>Wdrożenie programu PowerShell

Za pomocą `Set-AzVMExtension` polecenia można wdrożyć rozszerzenie maszyny wirtualnej agenta zależności na istniejącej maszynie wirtualnej. Przed uruchomieniem polecenia konfiguracje publiczne i prywatne muszą być przechowywane w tabeli mieszania programu PowerShell.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z witryny Azure portal i przy użyciu modułu Programu Azure PowerShell. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu modułu programu Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w plikach znalezionych w następującym katalogu:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Można też zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**. Aby uzyskać informacje dotyczące korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).
