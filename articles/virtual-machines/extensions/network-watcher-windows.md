---
title: Rozszerzenie maszyny wirtualnej Azure Agent obserwatorów sieci dla systemu Windows | Dokumentacja firmy Microsoft
description: Wdróż agenta monitora sieci na maszynie wirtualnej z systemem Windows przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 29f346b2a42f8d12e26bd59fbab86d763d3f29f0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Rozszerzenie maszyny wirtualnej obserwatorów agenta sieciowe dla systemu Windows

## <a name="overview"></a>Przegląd

[Azure obserwatora sieciowego](../../network-watcher/network-watcher-monitoring-overview.md) to sieci performance monitoring, Diagnostyka i analiza usługa, która umożliwia monitorowanie sieci platformy Azure. Rozszerzenie maszyny wirtualnej Agent monitora sieci jest wymagana do przechwytywania ruchu sieciowego na żądanie i inne zaawansowane funkcje na maszynach wirtualnych Azure.


Ten dokument zawiera szczegóły dotyczące obsługiwanych platform i opcje wdrażania dla rozszerzenia maszyny wirtualnej sieci obserwatorów agenta dla systemu Windows. Instalacja agenta nie zakłócać lub wymaga ponownego uruchomienia, maszyny wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie sieci obserwatorów agenta dla systemu Windows mogą być uruchamiane na systemie Windows Server 2008 R2, 2012 i 2012 R2, 2016 wersje. Nano Server nie jest obsługiwana w tej chwili.

### <a name="internet-connectivity"></a>Łączność z Internetem

Niektóre funkcje Agent monitora sieci wymaga, czy docelowa maszyna wirtualna jest połączony z Internetem. Bez możliwości nawiązywania połączeń wychodzących Agent monitora sieci nie będzie przekazywać przechwytywania pakietów do swojego konta magazynu. Aby uzyskać więcej informacji, zobacz [dokumentacji obserwatora sieciowego](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujące JSON zawiera schemat rozszerzenia Agent monitora sieci. Rozszerzenie nie wymaga, nie obsługuje ustawienia dostarczone przez użytkownika i zależy od konfiguracji domyślnej.

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
| Wydawcy | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Można wdrożyć rozszerzeń maszyny Wirtualnej platformy Azure przy użyciu szablonów usługi Azure Resource Manager. Schematu JSON szczegółowo opisane w poprzedniej sekcji, w szablonie usługi Azure Resource Manager umożliwia uruchomienie rozszerzenia sieci obserwatorów agenta podczas wdrażania szablonu usługi Azure Resource Manager.

## <a name="powershell-deployment"></a>Wdrożenie programu PowerShell

Użyj `Set-AzureRmVMExtension` polecenie, aby wdrożyć rozszerzenie maszyny wirtualnej sieci obserwatora agenta do istniejącej maszyny wirtualnej:

```powershell
Set-AzureRmVMExtension `
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

Dane dotyczące stanu wdrożenia rozszerzenia można pobrać z portalu Azure i programu PowerShell. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu modułu Azure PowerShell:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Dane wyjściowe wykonania rozszerzenia jest rejestrowany pliki znajdujące się w następującym katalogu:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, można zapoznać się z dokumentacją Podręcznik użytkownika obserwatora sieci lub skontaktuj się z ekspertami Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z platformy Azure obsługuje, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
