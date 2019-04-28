---
title: Rozszerzenie maszyny wirtualnej platformy Azure Agent usługi Network Watcher dla Windows | Dokumentacja firmy Microsoft
description: Wdróż Agent usługi Network Watcher na maszynie wirtualnej Windows przy użyciu rozszerzenia maszyny wirtualnej.
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
ms.openlocfilehash: 6e02f5a5b42da9c99a08782903cdc05ee32ec9d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743330"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Rozszerzenie maszyny wirtualnej agenta obserwatora sieciowe dla Windows

## <a name="overview"></a>Omówienie

[Usługa Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) to sieci wydajności monitorowania i diagnostyki i analizy usługa, która umożliwia monitorowanie sieci platformy Azure. Agent usługi Network Watcher rozszerzenie maszyny wirtualnej jest wymagana do przechwytywania ruchu sieciowego na żądanie i inne zaawansowane funkcje, na maszynach wirtualnych platformy Azure.


W tym dokumencie przedstawiono obsługiwane platformy i opcji wdrażania dla rozszerzenia maszyny wirtualnej Agent usługi Network Watcher Windows. Instalacja agenta nie zakłócać lub konieczne jest ponowne uruchomienie maszyny wirtualnej. Rozszerzenie można wdrożyć na maszyny wirtualne, które można wdrożyć. Jeśli maszyna wirtualna jest wdrażana przez usługi platformy Azure, sprawdź w dokumentacji usługi ustalić, czy nie pozwala na instalowanie rozszerzeń w maszynie wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Agent usługi Network Watcher rozszerzenia dla Windows mogą być uruchamiane względem systemu Windows Server 2008 R2, 2012, 2012 R2 i 2016 zwalnia. Serwer nano Server nie jest obsługiwana w tej chwili.

### <a name="internet-connectivity"></a>Łączność z Internetem

Niektóre funkcje Agent usługi Network Watcher wymaga, aby docelowej maszyny wirtualnej były połączone z Internetem. Bez możliwość nawiązywania połączeń wychodzących Agent usługi Network Watcher nie będą mogli przekazywać przechwytywania pakietów do swojego konta magazynu. Aby uzyskać więcej informacji, zobacz [dokumentacji usługi Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujący kod JSON zawiera schemat dla rozszerzenia Agent usługi Network Watcher. Rozszerzenie nie wymaga, ani nie obsługuje ustawienia dostarczone przez użytkownika i zależy od konfiguracji domyślnej.

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

Można wdrożyć rozszerzeń maszyny Wirtualnej platformy Azure przy użyciu szablonów usługi Azure Resource Manager. Schemat JSON szczegółowo opisane w poprzedniej sekcji w szablonie usługi Azure Resource Manager umożliwia rozszerzenie Agent usługi Network Watcher są uruchamiane podczas wdrażania szablonu usługi Azure Resource Manager.

## <a name="powershell-deployment"></a>Wdrożenie programu PowerShell

Użyj `Set-AzVMExtension` polecenie, aby wdrożyć rozszerzenie maszyny wirtualnej Agent usługi Network Watcher do istniejącej maszyny wirtualnej:

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

Dane dotyczące stanu wdrożeń rozszerzenia można pobrać z witryny Azure portal i programu PowerShell. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie, używając modułu Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Dane wyjściowe wykonywania rozszerzenia jest rejestrowany wpis pliki znajdujące się w następującym katalogu:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, można zapoznać się z dokumentacją Przewodnik po użytkownik obserwatora sieci lub skontaktuj się z pomocą ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
