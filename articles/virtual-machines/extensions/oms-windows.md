---
title: Log Analytics rozszerzenie maszyny wirtualnej dla systemu Windows
description: Wdróż agenta Log Analytics na maszynie wirtualnej z systemem Windows przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/30/2020
ms.author: akjosh
ms.openlocfilehash: a0b5f45ba993033a9f56ee6b078193677b1c5aad
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383395"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Log Analytics rozszerzenie maszyny wirtualnej dla systemu Windows

Dzienniki Azure Monitor udostępniają możliwości monitorowania między zasobami w chmurze i lokalnymi. Rozszerzenie maszyny wirtualnej Log Analytics agenta dla systemu Windows jest publikowane i obsługiwane przez firmę Microsoft. Rozszerzenie instaluje agenta usługi Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyn wirtualnych do istniejącego obszaru roboczego usługi Log Analytics. Ten dokument zawiera szczegółowe informacje o obsługiwanych platformach, konfiguracjach i opcjach wdrażania dla rozszerzenia maszyny wirtualnej Log Analytics dla systemu Windows.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Szczegółowe informacje o obsługiwanych systemach operacyjnych Windows można znaleźć w artykule [Omówienie agenta log Analytics](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Wersja agenta i rozszerzenia maszyny Wirtualnej
Poniższa tabela zawiera mapowanie wersji rozszerzenia maszyny wirtualnej z systemem Windows Log Analytics i pakietu agenta Log Analytics dla każdej wersji. 

| Wersja pakietu Log Analytics Windows Agent | Log Analytics wersja rozszerzenia maszyny wirtualnej z systemem Windows | Data wydania | Informacje o wersji |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18018 | 1.0.18018 | Październik 2019 r. | <ul><li> Drobne poprawki błędów i ulepszenia stabilizacji </li></ul> |
| 10.20.18011 | 1.0.18011 | Lipiec 2019 r. | <ul><li> Drobne poprawki błędów i ulepszenia stabilizacji </li><li> Zwiększono MaxExpressionDepth do 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Czerwiec 2019 r. | <ul><li> Drobne poprawki błędów i ulepszenia stabilizacji </li><li> Dodano możliwość wyłączania poświadczeń domyślnych podczas nawiązywania połączenia z serwerem proxy (obsługa WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | Marzec 2019 r. | <ul><li>Niewielkie poprawki stabilizacji </li></ul> |
| 10.19.10006 | Nie dotyczy | Dec 2018 | <ul><li> Niewielkie poprawki stabilizacji </li></ul> | 
| 8.0.11136 | Nie dotyczy | Września 2018 |  <ul><li> Dodano obsługę wykrywania zmiany identyfikatora zasobu podczas przenoszenia maszyny wirtualnej </li><li> Dodano obsługę identyfikatora zasobu raportowania podczas korzystania z instalacji niezwiązanej z rozszerzeniem </li></ul>| 
| 8.0.11103 | Nie dotyczy |  Kwiecień 2018 r. | |
| 8.0.11081 | 1.0.11081 | Lis 2017 | | 
| 8.0.11072 | 1.0.11072 | Września 2017 | |
| 8.0.11049 | 1.0.11049 | 2017 lutego | |

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center automatycznie inicjuje agenta Log Analytics i łączy go z domyślnym obszarem roboczym Log Analytics subskrypcji platformy Azure. Jeśli używasz usługi Azure Security Center nie należy uruchamiać za pomocą procedury w tym dokumencie. Spowoduje to zastąpienie skonfigurowanego obszaru roboczego i przerwanie połączenia z Azure Security Center.

### <a name="internet-connectivity"></a>Łączność z Internetem
Rozszerzenie agenta Log Analytics dla systemu Windows wymaga, aby docelowa maszyna wirtualna była połączona z Internetem. 

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON przedstawia schemat rozszerzenia agenta Log Analytics. Rozszerzenie wymaga identyfikatora obszaru roboczego i klucza obszaru roboczego z docelowego obszaru roboczego Log Analytics. Można je znaleźć w ustawieniach obszaru roboczego w Azure Portal. Ponieważ klucz obszaru roboczego, powinny być traktowane jako poufne dane, powinny być przechowywane w chronionym ustawienia konfiguracji. Dane platformy Azure ustawienia rozszerzenia chronione maszyny Wirtualnej jest zaszyfrowany i odszyfrowane tylko na docelowej maszynie wirtualnej. Należy pamiętać, że w **Identyfikator obszaru roboczego** i **workspaceKey** jest rozróżniana wielkość liter.

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
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| typ | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (np.)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (np.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* identyfikator obszaru roboczego jest nazywany consumerId w interfejsie API Log Analytics.

> [!NOTE]
> Aby uzyskać dodatkowe właściwości, zobacz Azure [Connect komputery z systemem Windows do Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Schemat JSON opisany w poprzedniej sekcji można użyć w szablonie Azure Resource Manager, aby uruchomić rozszerzenie agenta Log Analytics podczas wdrażania szablonu Azure Resource Manager. Przykładowy szablon zawierający rozszerzenie maszyny wirtualnej agenta Log Analytics można znaleźć w [galerii szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>Szablon nie obsługuje określania więcej niż jednego identyfikatora obszaru roboczego i klucza obszaru roboczego, jeśli chcesz skonfigurować agenta do raportowania do wielu obszarów roboczych. Aby skonfigurować agenta do raportowania do wielu obszarów roboczych, zobacz [Dodawanie lub usuwanie obszaru roboczego](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

KOD JSON rozszerzenia maszyny wirtualnej może być zagnieżdżony w obrębie zasobu maszyny wirtualnej lub umieszczony na głównym lub najwyższym poziomie szablonu JSON Menedżer zasobów. Położenie pliku JSON wpływa na wartość nazwy zasobu i typu. Aby uzyskać więcej informacji, zobacz [Ustawianie nazwy i typu dla zasobów podrzędnych](../../azure-resource-manager/templates/child-resource-name-type.md). 

W poniższym przykładzie założono, że rozszerzenie Log Analytics jest zagnieżdżone w ramach zasobu maszyny wirtualnej. Podczas zagnieżdżania zasobu rozszerzenia kod JSON jest umieszczany w obiekcie `"resources": []` maszyny wirtualnej.


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

## <a name="powershell-deployment"></a>Wdrażanie programu PowerShell

Za pomocą polecenia `Set-AzVMExtension` można wdrożyć rozszerzenie maszyny wirtualnej agenta Log Analytics do istniejącej maszyny wirtualnej. Przed uruchomieniem polecenia należy zapisać konfigurację publiczną i prywatną w tabeli skrótów programu PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
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

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu modułu Azure PowerShell. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie przy użyciu modułu Azure PowerShell.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w plikach znalezionych w następującym katalogu:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).
