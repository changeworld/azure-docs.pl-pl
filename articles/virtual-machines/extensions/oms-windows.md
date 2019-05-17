---
title: Rozszerzenie maszyny wirtualnej platformy Azure Monitor dla Windows | Dokumentacja firmy Microsoft
description: Wdróż agenta usługi Log Analytics na maszynie wirtualnej Windows przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/29/2019
ms.author: roiyz
ms.openlocfilehash: 270b3ae49a815c9e12fce9377c8298192237f28a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790374"
---
# <a name="azure-monitor-virtual-machine-extension-for-windows"></a>Rozszerzenie maszyny wirtualnej platformy Azure Monitor dla Windows

Dzienniki platformy Azure Monitor zapewnia możliwości monitorowania w zasoby w chmurze i lokalnych. Rozszerzenie maszyny wirtualnej agenta usługi Log Analytics dla Windows publikowana i obsługiwane przez firmę Microsoft. Rozszerzenie instaluje agenta usługi Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyn wirtualnych do istniejącego obszaru roboczego usługi Log Analytics. W tym dokumencie przedstawiono obsługiwanych platform, konfiguracji i opcje wdrażania dla rozszerzenia maszyny wirtualnej usługi Azure Monitor Windows.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie agenta usługi Log Analytics dla Windows obsługuje następujące wersje systemu operacyjnego Windows:

- Windows Server 2019
- Windows Server 2008 R2, 2012, 2012 R2, 2016 w wersji 1709 i 1803

### <a name="agent-and-vm-extension-version"></a>Wersja agenta i rozszerzenia maszyny Wirtualnej
Poniższa tabela zawiera mapowanie wersję rozszerzenia maszyny Wirtualnej platformy Azure Monitor i pakietu agenta usługi Log Analytics dla każdej wersji. 

| Wersja rozszerzenia usługi Azure Monitor maszyny Wirtualnej z systemem Linux | Wersja pakietu agenta analizy dziennika | Data wydania | Informacje o wersji |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 8.0.11049.0 | 1.0.11049.1 | Lutego 2017 r. | |
| 8.0.11072.0 | 1.0.11072.1 | Września 2017 r. | |
| 8.0.11081.0 | 1.0.11081.5 | Listopada 2017 r. | | 
| 8.0.11103.0 | Nie dotyczy |  Kwiecień 2018 r. | |
| 8.0.11136.0 | Nie dotyczy | Września 2018 r. |  <ul><li> Dodano obsługę wykrywania zmianę Identyfikatora zasobu na przeniesienie maszyny Wirtualnej </li><li> Dodano obsługę raportowania zasobów, zainstaluj identyfikator podczas korzystania bez rozszerzeń </li></ul>| 
| 10.19.10006.0 | Nie dotyczy | Grudnia 2018 r. | <ul><li> Stabilizacji drobne poprawki </li></ul> | 
| 10.19.13515.0 | 1.0.13515.1 | Marca 2019 r | <ul><li>Stabilizacji drobne poprawki </li></ul> |

### <a name="azure-security-center"></a>Azure Security Center

Usługa Azure Security Center automatycznie aprowizuje agenta usługi Log Analytics i łączy ją z obszarem roboczym usługi Log Analytics domyślnej subskrypcji platformy Azure. Jeśli używasz usługi Azure Security Center nie należy uruchamiać za pomocą procedury w tym dokumencie. Spowoduje to zastąpienie skonfigurowany obszar roboczy i przerwania połączenia z usługą Azure Security Center.

### <a name="internet-connectivity"></a>Łączność z Internetem
Rozszerzenie agenta usługi Log Analytics dla Windows wymaga, że docelowej maszyny wirtualnej jest połączony z Internetem. 

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujący kod JSON zawiera schemat dla rozszerzenia agenta usługi Log Analytics. Rozszerzenie wymaga Identyfikatora obszaru roboczego i klucz obszaru roboczego w docelowy obszar roboczy usługi Log Analytics. Te można znaleźć w ustawieniach obszaru roboczego w witrynie Azure portal. Ponieważ klucz obszaru roboczego, powinny być traktowane jako poufne dane, powinny być przechowywane w chronionym ustawienia konfiguracji. Dane platformy Azure ustawienia rozszerzenia chronione maszyny Wirtualnej jest zaszyfrowany i odszyfrowane tylko na docelowej maszynie wirtualnej. Należy pamiętać, że **workspaceId** i **klucz workspaceKey** jest rozróżniana wielkość liter.

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
| workspaceId (e.g)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| klucz workspaceKey (np.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* Identyfikator obszaru roboczego jest nazywany consumerId w interfejsu API programu Log Analytics.

## <a name="template-deployment"></a>Wdrożenie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Schemat JSON szczegółowo opisane w poprzedniej sekcji może służyć w szablonie usługi Azure Resource Manager do uruchomienia rozszerzenia agenta usługi Log Analytics podczas wdrażania szablonu usługi Azure Resource Manager. Przykładowy szablon, który zawiera rozszerzenie maszyny Wirtualnej agenta usługi Log Analytics można znaleźć na [w galerii platformy Azure Szybki Start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>Szablon nie obsługuje określania więcej niż jeden identyfikator obszaru roboczego i klucz obszaru roboczego, jeśli chcesz skonfigurować agenta w celu raportowania do wielu obszarów roboczych. Aby skonfigurować agenta w celu raportowania do wielu obszarów roboczych, zobacz [Dodawanie lub usuwanie obszaru roboczego](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

Kod JSON dla rozszerzenia maszyny wirtualnej mogą być zagnieżdżone wewnątrz zasobu maszyny wirtualnej lub umieszczone w katalogu głównego lub najwyższego poziomu szablon JSON usługi Resource Manager. Rozmieszczanie za pomocą pliku JSON ma wpływ na wartości nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustaw nazwę i typ zasobów podrzędnych](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources). 

W poniższym przykładzie założono, że rozszerzenie usługi Azure Monitor jest zagnieżdżona w obrębie zasobu maszyny wirtualnej. Zagnieżdżanie rozszerzenia zasobu, za pomocą pliku JSON jest umieszczany w `"resources": []` obiektu maszyny wirtualnej.


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

`Set-AzVMExtension` Polecenie może służyć do wdrożenia rozszerzenia maszyny wirtualnej agenta usługi Log Analytics do istniejącej maszyny wirtualnej. Przed uruchomieniem polecenia, konfiguracje publicznymi i prywatnymi muszą być przechowywane w tabeli wyznaczania wartości skrótu programu PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
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
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Dane wyjściowe wykonywania rozszerzenia jest rejestrowany wpis pliki znajdujące się w następującym katalogu:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
