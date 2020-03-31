---
title: Rozszerzenie agenta usługi Azure Network Watcher dla maszyny wirtualnej dla systemu Windows
description: Wdrażanie agenta obserwatora sieci na maszynie wirtualnej systemu Windows przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 998e160edce25b9d466a1db090abcefeb7870172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073686"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Rozszerzenie maszyny wirtualnej agenta obserwatora sieci dla systemu Windows

## <a name="overview"></a>Omówienie

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) to usługa monitorowania wydajności sieci, diagnostyki i analizy, która umożliwia monitorowanie sieci platformy Azure. Rozszerzenie maszyny wirtualnej agenta obserwatora sieci jest wymagane do przechwytywania ruchu sieciowego na żądanie i innych zaawansowanych funkcji na maszynach wirtualnych platformy Azure.


Ten dokument zawiera szczegółowe informacje na temat obsługiwanych platform i opcji wdrażania rozszerzenia maszyny wirtualnej agenta funkcji Kontrola sieci dla systemu Windows. Instalacja agenta nie zakłóca ani nie wymaga ponownego uruchomienia maszyny wirtualnej. Rozszerzenie można wdrożyć na maszynach wirtualnych, które można wdrożyć. Jeśli maszyna wirtualna jest wdrażana przez usługę platformy Azure, sprawdź dokumentację usługi, aby ustalić, czy zezwala na instalowanie rozszerzeń na maszynie wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie Agenta obserwatora sieciowego dla systemu Windows można uruchomić w wersjach z systemami Windows Server 2008 R2, 2012, 2012 R2 i 2016. Serwer Nano Server nie jest obecnie obsługiwany.

### <a name="internet-connectivity"></a>Łączność z Internetem

Niektóre funkcje agenta obserwatora sieci wymaga, aby docelowa maszyna wirtualna była połączona z Internetem. Bez możliwości nawiązywania połączeń wychodzących agent obserwatora sieci nie będzie mógł przesyłać przechwytywania pakietów na konto magazynu. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją Obserwatora sieci.](../../network-watcher/network-watcher-monitoring-overview.md)

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniżej przedstawiono schemat rozszerzenia agenta obserwatora sieci. Rozszerzenie nie wymaga ani nie obsługuje żadnych ustawień dostarczonych przez użytkownika i opiera się na jego domyślnej konfiguracji.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentWindows",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość / Przykład |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| wydawca | Microsoft.Azure.NetworkWatcher |
| type | Usługa NetworkWatcherAgentWindows |
| typHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonów usługi Azure Resource Manager. Schemat JSON opisany w poprzedniej sekcji w szablonie usługi Azure Resource Manager umożliwia uruchomienie rozszerzenia agenta obserwatora sieci podczas wdrażania szablonu usługi Azure Resource Manager.

## <a name="powershell-deployment"></a>Wdrożenie programu PowerShell

Użyj `Set-AzVMExtension` polecenia, aby wdrożyć rozszerzenie maszyny wirtualnej agenta obserwatora sieci na istniejącej maszynie wirtualnej:

```powershell
Set-AzVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Można pobrać dane o stanie wdrożeń rozszerzeń z witryny Azure portal i programu PowerShell. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu modułu programu Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w plikach znalezionych w następującym katalogu:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie tego artykułu, możesz zapoznać się z dokumentacją podręcznika użytkownika obserwatora sieci lub skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje na temat korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).
