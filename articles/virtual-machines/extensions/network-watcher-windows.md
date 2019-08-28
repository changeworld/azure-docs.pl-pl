---
title: Rozszerzenie maszyny wirtualnej usługi Azure Network Watcher Agent dla systemu Windows | Microsoft Docs
description: Wdróż agenta Network Watcher na maszynie wirtualnej z systemem Windows przy użyciu rozszerzenia maszyny wirtualnej.
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
ms.openlocfilehash: 77685fd6549906cfb050e12d53ec151c964fda42
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084413"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Rozszerzenie maszyny wirtualnej agenta Network Watcher dla systemu Windows

## <a name="overview"></a>Przegląd

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) to usługa monitorowania wydajności sieci, diagnostyki i analizy, która umożliwia monitorowanie sieci platformy Azure. Rozszerzenie maszyny wirtualnej agenta Network Watcher jest wymaganiem do przechwytywania ruchu sieciowego na żądanie oraz innych zaawansowanych funkcji na maszynach wirtualnych platformy Azure.


Ten dokument zawiera szczegółowe informacje na temat obsługiwanych platform i opcji wdrażania dla rozszerzenia maszyny wirtualnej agenta Network Watcher dla systemu Windows. Instalacja agenta nie zakłóca działania lub wymaga ponownego uruchomienia maszyny wirtualnej. Rozszerzenie można wdrożyć na maszynach wirtualnych, które wdrażasz. Jeśli maszyna wirtualna jest wdrażana przez usługę platformy Azure, zapoznaj się z dokumentacją usługi, aby określić, czy zezwala ona na instalowanie rozszerzeń na maszynie wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie agenta Network Watcher dla systemu Windows może być uruchamiane w systemie Windows Server 2008 R2, 2012, 2012 R2 i 2016. Serwer nano Server nie jest w tej chwili obsługiwany.

### <a name="internet-connectivity"></a>Łączność z Internetem

Niektóre funkcje agenta Network Watcher wymagają, aby docelowa maszyna wirtualna była połączona z Internetem. Bez możliwości nawiązywania połączeń wychodzących Agent Network Watcher nie będzie w stanie przekazywać przechwycenia pakietu do konta magazynu. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON przedstawia schemat rozszerzenia agenta Network Watcher. Rozszerzenie nie wymaga ani nie obsługuje żadnych ustawień dostarczonych przez użytkownika i zależy od konfiguracji domyślnej.

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

| Name (Nazwa) | Wartość / przykład |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać za pomocą szablonów Azure Resource Manager. Można użyć schematu JSON szczegółowego w poprzedniej sekcji w szablonie Azure Resource Manager, aby uruchomić rozszerzenie agenta Network Watcher podczas wdrażania szablonu Azure Resource Manager.

## <a name="powershell-deployment"></a>Wdrażanie programu PowerShell

`Set-AzVMExtension` Użyj polecenia, aby wdrożyć rozszerzenie maszyny wirtualnej agenta Network Watcher na istniejącej maszynie wirtualnej:

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

Możesz pobrać dane dotyczące stanu wdrożeń rozszerzeń z Azure Portal i programu PowerShell. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie przy użyciu modułu Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w plikach znalezionych w następującym katalogu:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz zapoznać się z dokumentacją podręcznika użytkownika Network Watcher lub skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
