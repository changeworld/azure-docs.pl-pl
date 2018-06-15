---
title: Rozszerzenie maszyny wirtualnej Azure Agent obserwatorów sieci dla systemu Linux | Dokumentacja firmy Microsoft
description: Wdróż agenta monitora sieci na maszynie wirtualnej systemu Linux przy użyciu rozszerzenia maszyny wirtualnej.
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
ms.openlocfilehash: db508e2311602a66a2c252ffaa842f8bfb4f670b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076075"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Rozszerzenie maszyny wirtualnej obserwatorów agenta sieciowe dla systemu Linux

## <a name="overview"></a>Przegląd

[Azure obserwatora sieciowego](/azure/network-watcher/) jest sieci performance monitoring, Diagnostyka i analiza usługa, która umożliwia monitorowanie sieci platformy Azure. Rozszerzenie maszyny wirtualnej (VM) Agent monitora sieci jest wymagana do niektórych funkcji obserwatora sieciowego na maszynach wirtualnych Azure, takich jak Przechwytywanie ruchu sieciowego na żądanie i inne zaawansowane funkcje.

Ten artykuł zawiera szczegóły dotyczące obsługiwanych platform i opcje wdrażania dla rozszerzenia maszyny Wirtualnej agenta obserwatorów sieci dla systemu Linux. Instalacja agenta nie zakłócać lub wymaga ponownego uruchomienia, maszyny wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie Agent monitora sieci można skonfigurować dla następujących dystrybucje systemu Linux:

| Dystrybucja | Wersja |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS i 12.04 LTS |
| Debian | 7 i 8 |
| RedHat | 6 i 7 |
| Oracle Linux | 6.8 + i 7 |
| SUSE Linux Enterprise Server | 11 i 12 |
| OpenSUSE przestępnego | 42.3 + |
| CentOS | 6.5 + i 7 |
| CoreOS | 899.17.0+ |

CoreOS nie jest obsługiwane.

### <a name="internet-connectivity"></a>Łączność z Internetem

Niektóre funkcje Agent monitora sieci wymaga, czy maszyna wirtualna jest połączony z Internetem. Bez możliwości nawiązywania połączeń wychodzących niektóre funkcje sieciowe obserwatorów agenta może nie działać poprawnie lub staną się niedostępne. Aby uzyskać więcej informacji o funkcji obserwatora sieciowego wymaga agenta, zobacz[dokumentacji obserwatora sieciowego](/azure/network-watcher/).

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujące JSON zawiera schemat rozszerzenia Agent monitora sieci. Rozszerzenie nie wymagają lub obsługuje wszystkie ustawienia dostarczone przez użytkownika. Rozszerzenie zależy od konfiguracji domyślnej.

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

Można wdrożyć rozszerzeń maszyny Wirtualnej platformy Azure z szablonem usługi Azure Resource Manager. Aby wdrożyć rozszerzenie sieci obserwatorów agenta, należy użyć poprzedniej schematu json w szablonie.

## <a name="azure-cli-10-deployment"></a>Wdrożenia usługi Azure CLI w wersji 1.0

Poniższy przykład wdraża rozszerzenia sieci obserwatorów agenta z maszyny Wirtualnej na istniejącej maszyny Wirtualnej wdrożone za pośrednictwem klasycznego modelu wdrażania:

```azurecli
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-20-deployment"></a>Wdrożenia usługi Azure CLI 2.0

Poniższy przykład wdraża rozszerzenia sieci obserwatorów agenta z maszyny Wirtualnej na istniejącej maszyny Wirtualnej wdrożone za pomocą Menedżera zasobów:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Można pobrać danych o stanie wdrożenia rozszerzenia przy użyciu portalu Azure lub interfejsu wiersza polecenia Azure.

Poniższy przykład przedstawia stan wdrażania rozszerzeń dla maszyny Wirtualnej wdrożone za pośrednictwem klasycznego modelu wdrażania, przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure:

```azurecli
azure config mode asm
azure vm extension get myVM1
```
Dane wyjściowe wykonania rozszerzenia jest rejestrowany pliki znajdujące się w następującym katalogu:

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

Poniższy przykład przedstawia stan wdrożenia rozszerzenia NetworkWatcherAgentLinux dla maszyny Wirtualnej wdrożone za pomocą Menedżera zasobów za pomocą 2.0 interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, może się odwoływać do [dokumentacji obserwatora sieciowego](/azure/network-watcher/), lub skontaktuj się z ekspertami Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskać pomoc techniczną**. Aby uzyskać informacje o korzystaniu z platformy Azure obsługuje, zobacz [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
