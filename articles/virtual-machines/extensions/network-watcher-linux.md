---
title: Rozszerzenie maszyny wirtualnej platformy Azure Agent usługi Network Watcher dla systemu Linux | Dokumentacja firmy Microsoft
description: Wdróż Agent usługi Network Watcher na maszynie wirtualnej systemu Linux przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-linux
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 5ed5e791cd6e611218769650115c78afd1869f67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743433"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Rozszerzenie maszyny wirtualnej agenta obserwatora sieciowe dla systemu Linux

## <a name="overview"></a>Omówienie

[Usługa Azure Network Watcher](/azure/network-watcher/) to sieci wydajności monitorowania i diagnostyki i analizy usługa, która umożliwia monitorowanie sieci platformy Azure. Rozszerzenie maszyny wirtualnej (VM) Agent usługi Network Watcher jest wymagana dla niektórych funkcji usługi Network Watcher na maszynach wirtualnych platformy Azure, takich jak Przechwytywanie ruchu sieciowego na żądanie i inne zaawansowane funkcje.

Ten artykuł szczegółowo opisuje obsługiwanych platformach i opcje wdrażania dla rozszerzenia maszyny Wirtualnej Network Watcher agenta dla systemu Linux. Instalacja agenta nie zakłócać lub konieczne jest ponowne uruchomienie maszyny wirtualnej. Rozszerzenie można wdrożyć na maszyny wirtualne, które można wdrożyć. Jeśli maszyna wirtualna jest wdrażana przez usługi platformy Azure, sprawdź w dokumentacji usługi ustalić, czy nie pozwala na instalowanie rozszerzeń w maszynie wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie Agent usługi Network Watcher można skonfigurować poniższe dystrybucje systemu Linux:

| Dystrybucja | Wersja |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 i 8 |
| Red Hat | 6 i 7 |
| Oracle Linux | 6.8 + i 7 |
| SUSE Linux Enterprise Server | 11 i 12 |
| Przestępnym OpenSUSE | 42.3+ |
| CentOS | 6.5 + i 7 |
| CoreOS | 899.17.0+ |


### <a name="internet-connectivity"></a>Łączność z Internetem

Niektóre funkcje Agent usługi Network Watcher wymaga, że maszyny Wirtualnej jest połączony z Internetem. Bez możliwość nawiązywania połączeń wychodzących niektóre funkcje Agent usługi Network Watcher może spowodować nieprawidłowe działanie lub staną się niedostępne. Aby uzyskać więcej informacji na temat funkcji usługi Network Watcher, który wymaga agenta, zobacz[dokumentacji usługi Network Watcher](/azure/network-watcher/).

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujący kod JSON zawiera schemat dla rozszerzenia Agent usługi Network Watcher. Rozszerzenie nie wymagają i obsługują wszystkie ustawienia dostarczone przez użytkownika. Rozszerzenie zależy od konfiguracji domyślnej.

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
        "type": "NetworkWatcherAgentLinux",
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
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Można wdrożyć rozszerzeń maszyny Wirtualnej platformy Azure przy użyciu szablonu usługi Azure Resource Manager. Aby wdrożyć rozszerzenie Agent usługi Network Watcher, należy użyć poprzedniego schematu json w szablonie.

## <a name="azure-classic-cli-deployment"></a>Klasycznym wdrożeniu platformy Azure CLI

Poniższy przykład służy do wdrażania rozszerzenia maszyny Wirtualnej Network Watcher agenta do istniejącej maszyny Wirtualnej wdrożone za pośrednictwem klasycznego modelu wdrażania:

```azurecli
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure

Poniższy przykład służy do wdrażania rozszerzenia maszyny Wirtualnej Network Watcher agenta do istniejącej maszyny Wirtualnej wdrożony za pomocą usługi Resource Manager:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Możesz pobrać dane dotyczące stanu wdrożeń rozszerzenia za pomocą witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.

Poniższy przykład przedstawia stan wdrażania rozszerzeń maszyny wirtualnej wdrożone za pośrednictwem klasycznego modelu wdrażania przy użyciu platformy Azure klasyczny interfejs wiersza polecenia:

```azurecli
azure config mode asm
azure vm extension get myVM1
```
Dane wyjściowe wykonywania rozszerzenia jest rejestrowany wpis pliki znajdujące się w następującym katalogu:

```
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
```

Poniższy kod przedstawia rozszerzenia NetworkWatcherAgentLinux stan wdrożenia dla maszyny Wirtualnej wdrożonej przy użyciu usługi Resource Manager przy użyciu wiersza polecenia platformy Azure:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz zapoznać się z [dokumentacji usługi Network Watcher](/azure/network-watcher/), lub skontaktuj się z pomocą ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskać pomoc techniczną**. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, zobacz [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
