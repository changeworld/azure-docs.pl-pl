---
title: Rozszerzenie maszyny wirtualnej OMS Azure dla systemu Windows | Dokumentacja firmy Microsoft
description: Wdróż agenta pakietu OMS na maszynie wirtualnej z systemem Windows przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danis
ms.openlocfilehash: c365c43eb5abb975bf77e28ad061ff091f5ec627
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="oms-virtual-machine-extension-for-windows"></a>Rozszerzenie maszyny wirtualnej OMS dla systemu Windows

Operations Management Suite (OMS) zapewnia możliwości korygowania monitorowania, alertów i alertów w chmurze i lokalnych zasobów. Agent pakietu OMS rozszerzenie maszyny wirtualnej dla systemu Windows publikowana i obsługiwane przez firmę Microsoft. Rozszerzenie instaluje agenta pakietu OMS na maszynach wirtualnych platformy Azure i rejestrowania maszyn wirtualnych w istniejącym obszarem roboczym pakietu OMS. Ten dokument zawiera szczegóły dotyczące obsługiwanych platform, konfiguracji i opcje wdrażania dla rozszerzenia maszyny wirtualnej OMS dla systemu Windows.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie Agent pakietu OMS dla systemu Windows mogą być uruchamiane na systemie Windows Server 2008 R2, 2012 i 2012 R2, 2016 wersje.

### <a name="azure-security-center"></a>Azure Security Center

Centrum zabezpieczeń Azure automatycznie udostępnia agent pakietu OMS i łączy go z obszaru roboczego analizy dzienników domyślne subskrypcji platformy Azure. Jeśli korzystasz z Centrum zabezpieczeń Azure, nie należy uruchamiać kroków w tym dokumencie. Spowoduje to zastąpienie obszaru roboczego skonfigurowana i podział połączenie z Centrum zabezpieczeń Azure.

### <a name="internet-connectivity"></a>Łączność z Internetem
Rozszerzenia Agent pakietu OMS dla systemu Windows wymaga, aby docelowa maszyna wirtualna jest połączony z Internetem. 

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujące JSON zawiera schemat rozszerzenia Agent pakietu OMS. Rozszerzenie wymaga obszaru roboczego identyfikator i klucz obszaru roboczego z docelowy obszar roboczy OMS, te można znaleźć w portalu OMS. Ponieważ klucz obszaru roboczego powinien być traktowany jako dane poufne, powinny być przechowywane w chronionej konfiguracji. Dane Azure ustawienia rozszerzenia chronione maszyny Wirtualnej jest szyfrowany i odszyfrowane tylko na docelowej maszynie wirtualnej. Należy pamiętać, że **workspaceId** i **workspaceKey** jest rozróżniana wielkość liter.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Wartości właściwości

| Name (Nazwa) | Wartość / przykład |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| Wydawcy | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (np.) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (np.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny Wirtualnej platformy Azure można wdrożyć przy użyciu szablonów usługi Azure Resource Manager. Schematu JSON szczegółowo opisane w poprzedniej sekcji można w szablonie usługi Azure Resource Manager rozszerzenia Agent pakietu OMS są uruchamiane podczas wdrażania szablonu usługi Azure Resource Manager. Przykładowy szablon, który uwzględnia również rozszerzenie maszyny Wirtualnej agenta pakietu OMS można znaleźć w [Azure Szybki Start galerii](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

JSON dla rozszerzenia maszyny wirtualnej mogą być zagnieżdżone wewnątrz zasobu maszyny wirtualnej lub umieszczony w katalogu głównego lub najwyższego poziomu szablonu usługi Resource Manager JSON. Umieszczanie JSON ma wpływ na wartość nazwy zasobów i typu. Aby uzyskać więcej informacji, zobacz [Ustaw nazwę i typ zasoby podrzędne](../../azure-resource-manager/resource-manager-templates-resources.md#child-resources). 

Poniższy przykład przyjęto założenie, że rozszerzenie OMS jest zagnieżdżona zasobu maszyny wirtualnej. Podczas zagnieżdżania rozszerzenia zasobu, JSON jest umieszczany w `"resources": []` obiektu maszyny wirtualnej.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Podczas umieszczania rozszerzenia JSON w elemencie głównym szablonu, nazwy zasobu zawiera odwołanie do nadrzędnego maszyny wirtualnej, a typ odzwierciedla zagnieżdżonych. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>Wdrożenie programu PowerShell

`Set-AzureRmVMExtension` Polecenia można wdrożyć agenta pakietu OMS rozszerzenie maszyny wirtualnej na istniejącej maszyny wirtualnej. Przed uruchomieniem polecenia, konfiguracje publicznymi i prywatnymi muszą być przechowywane w tablicy skrótów programu PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzureRmVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i obsługa techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożenia rozszerzenia może zostać pobrany z portalu Azure i przy użyciu modułu Azure PowerShell. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu modułu Azure PowerShell.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Dane wyjściowe wykonania rozszerzenia jest rejestrowany pliki znajdujące się w następującym katalogu:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z platformy Azure obsługuje, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
