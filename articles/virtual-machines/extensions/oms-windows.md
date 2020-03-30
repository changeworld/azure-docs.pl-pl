---
title: Rozszerzenie maszyny wirtualnej usługi Log Analytics dla systemu Windows
description: Wdrażanie agenta analizy dzienników na maszynie wirtualnej systemu Windows przy użyciu rozszerzenia maszyny wirtualnej.
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
ms.openlocfilehash: 85977819d30ddc8745eb9231242eb1990222676c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530992"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Rozszerzenie maszyny wirtualnej usługi Log Analytics dla systemu Windows

Dzienniki usługi Azure Monitor udostępnia możliwości monitorowania w zasobach w chmurze i lokalnie. Rozszerzenie maszyny wirtualnej agenta usługi Log Analytics dla systemu Windows jest publikowane i obsługiwane przez firmę Microsoft. Rozszerzenie instaluje agenta usługi Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyny wirtualne w istniejącym obszarze roboczym usługi Log Analytics. Ten dokument zawiera szczegółowe informacje na temat obsługiwanych platform, konfiguracji i opcji wdrażania rozszerzenia maszyny wirtualnej usługi Log Analytics dla systemu Windows.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Szczegółowe informacje na temat obsługiwanych systemów operacyjnych Windows można znaleźć w [artykule — agenta usługi Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)

### <a name="agent-and-vm-extension-version"></a>Wersja rozszerzenia agenta i maszyny Wirtualnej
Poniższa tabela zawiera mapowanie wersji rozszerzenia maszyny Wirtualnej analizy dziennika systemu Windows i pakietu agenta analizy dzienników dla każdej wersji. 

| Wersja pakietu agenta systemu Windows usługi Log Analytics | Wersja rozszerzenia maszyny Wirtualnej systemu Windows usługi Log Analytics | Data wydania | Informacje o wersji |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18029 | 1.0.18029 | Marzec 2020 r.   | <ul><li>Dodaje obsługę podpisywania kodu SHA-2</li><li>Usprawnia instalację rozszerzeń maszyn wirtualnych i zarządzanie nimi</li><li>Rozwiązuje błąd w integracji programu Azure Arc for Servers</li><li>Dodaje wbudowane narzędzie do rozwiązywania problemów dla obsługi klienta</li><li>Dodaje obsługę dodatkowych regionów platformy Azure dla instytucji rządowych</li> |
| 10.20.18018 | 1.0.18018 | Październik 2019 r. | <ul><li> Drobne poprawki błędów i ulepszenia stabilizacji </li></ul> |
| 10.20.18011 | 1.0.18011 | Lipiec 2019 r. | <ul><li> Drobne poprawki błędów i ulepszenia stabilizacji </li><li> Zwiększono MaxExpressionDepth do 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Czerwiec 2019 r. | <ul><li> Drobne poprawki błędów i ulepszenia stabilizacji </li><li> Dodano możliwość wyłączania poświadczeń domyślnych podczas nawiązywania połączenia z serwerem proxy (obsługa WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | Marzec 2019 r. | <ul><li>Drobne poprawki stabilizacyjne </li></ul> |
| 10.19.10006 | Nie dotyczy | Grudzień 2018 r. | <ul><li> Drobne poprawki stabilizacyjne </li></ul> | 
| 8.0.11136 | Nie dotyczy | Wrzesień 2018 r. |  <ul><li> Dodano obsługę wykrywania zmiany identyfikatora zasobu podczas przenoszenia maszyny Wirtualnej </li><li> Dodano obsługę raportowania identyfikatora zasobu podczas korzystania z instalacji bez rozszerzenia </li></ul>| 
| 8.0.11103 | Nie dotyczy |  Kwiecień 2018 r. | |
| 8.0.11081 | 1.0.11081 | Listopad 2017 r. | | 
| 8.0.11072 | 1.0.11072 | Wrzesień 2017 r. | |
| 8.0.11049 | 1.0.11049 | Luty 2017 r. | |


### <a name="azure-security-center"></a>Azure Security Center

Usługa Azure Security Center automatycznie udostępnia agentowi usługi Log Analytics i łączy go z domyślnym obszarem roboczym usługi Log Analytics subskrypcji platformy Azure. Jeśli używasz usługi Azure Security Center, nie należy uruchamiać kroków w tym dokumencie. W ten sposób zastępuje skonfigurowany obszar roboczy i przerywa połączenie z usługą Azure Security Center.

### <a name="internet-connectivity"></a>Łączność z Internetem
Rozszerzenie agenta usługi Log Analytics dla systemu Windows wymaga, aby docelowa maszyna wirtualna była połączona z Internetem. 

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniżej przedstawiono schemat rozszerzenia agenta usługi Log Analytics. Rozszerzenie wymaga identyfikatora obszaru roboczego i klucza obszaru roboczego z docelowego obszaru roboczego usługi Log Analytics. Można je znaleźć w ustawieniach obszaru roboczego w witrynie Azure portal. Ponieważ klucz obszaru roboczego powinien być traktowany jako poufne dane, powinien być przechowywany w konfiguracji ustawień chronionych. Dane ustawień chronionych przez rozszerzenie maszyny Wirtualnej platformy Azure są szyfrowane i odszyfrowywane tylko na docelowej maszynie wirtualnej. Należy zauważyć, **że identyfikator workspaceId** i **klawisz workspaceKey** są rozróżniane.

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

| Nazwa | Wartość / Przykład |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| wydawca | Monitorowanie usługi Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typHandlerVersion | 1.0 |
| obszar roboczyId (np.)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (np.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\*Obszar roboczyId jest nazywany consumerId w interfejsie API usługi Log Analytics.

> [!NOTE]
> Aby uzyskać dodatkowe właściwości, zobacz Łączenie [komputerów z systemem Windows](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)z usługą Azure Monitor .

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonów usługi Azure Resource Manager. Schemat JSON wyszczególniony w poprzedniej sekcji może służyć w szablonie usługi Azure Resource Manager do uruchamiania rozszerzenia agenta usługi Log Analytics podczas wdrażania szablonu usługi Azure Resource Manager. Przykładowy szablon zawierający rozszerzenie maszyny Wirtualnej agenta usługi Log Analytics można znaleźć w [Galerii Szybki start platformy Azure.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm) 

>[!NOTE]
>Szablon nie obsługuje określania więcej niż jednego identyfikatora obszaru roboczego i klucza obszaru roboczego, jeśli chcesz skonfigurować agenta do raportowania do wielu obszarów roboczych. Aby skonfigurować agenta do raportowania do wielu obszarów roboczych, zobacz [Dodawanie lub usuwanie obszaru roboczego](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

JSON dla rozszerzenia maszyny wirtualnej może być zagnieżdżony wewnątrz zasobu maszyny wirtualnej lub umieszczone na głównym lub najwyższym poziomie szablonu JSON Menedżera zasobów. Położenie JSON wpływa na wartość nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustawianie nazwy i typu zasobów podrzędnych](../../azure-resource-manager/templates/child-resource-name-type.md). 

W poniższym przykładzie przyjęto założenie, że rozszerzenie usługi Log Analytics jest zagnieżdżone wewnątrz zasobu maszyny wirtualnej. Podczas zagnieżdżania zasobu rozszerzenia `"resources": []` JSON jest umieszczany w obiekcie maszyny wirtualnej.


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

Podczas umieszczania rozszerzenia JSON w katalogu głównym szablonu nazwa zasobu zawiera odwołanie do nadrzędnej maszyny wirtualnej, a typ odzwierciedla konfigurację zagnieżdżoną. 

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

Polecenie `Set-AzVMExtension` może służyć do wdrażania rozszerzenia maszyny wirtualnej agenta usługi Log Analytics do istniejącej maszyny wirtualnej. Przed uruchomieniem polecenia konfiguracje publiczne i prywatne muszą być przechowywane w tabeli mieszania programu PowerShell. 

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

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane o stanie wdrożeń rozszerzeń można pobrać z witryny Azure portal i przy użyciu modułu programu Azure PowerShell. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu modułu programu Azure PowerShell.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w plikach znalezionych w następującym katalogu:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje na temat korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).
