---
title: Rozszerzenie maszyny wirtualnej platformy Azure Log Analytics dla Windows | Dokumentacja firmy Microsoft
description: Wdróż agenta usługi Log Analytics na maszynie wirtualnej Windows przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: zroiy
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
ms.author: roiyz
ms.openlocfilehash: 2c228784e7a0a78f6dfdb93ce632bf340dea0ee9
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414746"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Dziennik analizy rozszerzenia maszyny wirtualnej, aby uzyskać Windows

Usługa log Analytics oferuje funkcje monitorowania w chmurze i zasobów lokalnych. Rozszerzenie maszyny wirtualnej Log Analytics Agent for Windows publikowana i obsługiwane przez firmę Microsoft. Rozszerzenie instaluje agenta usługi Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyn wirtualnych do istniejącego obszaru roboczego usługi Log Analytics. W tym dokumencie przedstawiono obsługiwanych platform, konfiguracji i opcje wdrażania dla rozszerzenia maszyny wirtualnej usługi Log Analytics Windows.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenia Log Analytics Agent for Windows mogą być uruchamiane względem systemu Windows Server 2008 R2, 2012, 2012 R2 i 2016 zwalnia.

### <a name="azure-security-center"></a>Azure Security Center

Usługa Azure Security Center automatycznie aprowizuje agenta usługi Log Analytics i łączy ją z obszarem roboczym analizy dzienników domyślnej subskrypcji platformy Azure. Jeśli używasz usługi Azure Security Center nie należy uruchamiać za pomocą procedury w tym dokumencie. Spowoduje to zastąpienie skonfigurowany obszar roboczy i przerwania połączenia z usługą Azure Security Center.

### <a name="internet-connectivity"></a>Łączność z Internetem
Rozszerzenia Log Analytics Agent for Windows wymaga, że docelowej maszyny wirtualnej jest połączony z Internetem. 

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujący kod JSON zawiera schemat dla rozszerzenia Log Analytics Agent. Rozszerzenie wymaga identyfikatora obszaru roboczego i klucz obszaru roboczego w docelowy obszar roboczy usługi Log Analytics. Te można znaleźć w ustawieniach obszaru roboczego w witrynie Azure portal. Ponieważ klucz obszaru roboczego, powinny być traktowane jako poufne dane, powinny być przechowywane w chronionym ustawienia konfiguracji. Dane platformy Azure ustawienia rozszerzenia chronione maszyny Wirtualnej jest zaszyfrowany i odszyfrowane tylko na docelowej maszynie wirtualnej. Należy pamiętać, że **workspaceId** i **klucz workspaceKey** jest rozróżniana wielkość liter.

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
| Identyfikator obszaru roboczego (np.) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| klucz workspaceKey (np.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Schemat JSON szczegółowo opisane w poprzedniej sekcji może służyć w szablonie usługi Azure Resource Manager do uruchomienia rozszerzenia Log Analytics Agent podczas wdrażania szablonu usługi Azure Resource Manager. Przykładowy szablon, który uwzględnia również rozszerzenie Log Analytics Agent VM znajduje się na [w galerii platformy Azure Szybki Start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

Kod JSON dla rozszerzenia maszyny wirtualnej mogą być zagnieżdżone wewnątrz zasobu maszyny wirtualnej lub umieszczone w katalogu głównego lub najwyższego poziomu szablon JSON usługi Resource Manager. Rozmieszczanie za pomocą pliku JSON ma wpływ na wartości nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustaw nazwę i typ zasobów podrzędnych](../../azure-resource-manager/resource-manager-templates-resources.md#child-resources). 

W poniższym przykładzie założono, że rozszerzenia Log Analytics jest zagnieżdżona w obrębie zasobu maszyny wirtualnej. Zagnieżdżanie rozszerzenia zasobu, za pomocą pliku JSON jest umieszczany w `"resources": []` obiektu maszyny wirtualnej.


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

Podczas umieszczania rozszerzenia JSON w katalogu głównym szablonu, nazwa zasobu zawiera odwołanie do nadrzędnej maszyny wirtualnej, a typ odzwierciedla zagnieżdżonych. 

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

`Set-AzureRmVMExtension` Polecenie może służyć do wdrażania rozszerzenia Log Analytics Agent maszyny wirtualnej na istniejącej maszyny wirtualnej. Przed uruchomieniem polecenia, konfiguracje publicznymi i prywatnymi muszą być przechowywane w tabeli wyznaczania wartości skrótu programu PowerShell. 

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

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzenia można pobrać z witryny Azure portal i za pomocą modułu Azure PowerShell. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie, używając modułu Azure PowerShell.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Dane wyjściowe wykonywania rozszerzenia jest rejestrowany wpis pliki znajdujące się w następującym katalogu:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
