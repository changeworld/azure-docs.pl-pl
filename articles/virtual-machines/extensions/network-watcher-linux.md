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
ms.openlocfilehash: 5a33f183470ec3879344f0cfe335bab38f9ff30f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Rozszerzenie maszyny wirtualnej obserwatorów agenta sieciowe dla systemu Linux

## <a name="overview"></a>Przegląd

[Azure obserwatora sieciowego](/azure/network-watcher/) jest sieci performance monitoring, Diagnostyka i analiza usługa, która umożliwia monitorowanie sieci platformy Azure. Rozszerzenie maszyny wirtualnej sieci obserwatorów agenta jest wymagane dla niektórych funkcji obserwatora sieciowego na maszynach wirtualnych Azure. Dotyczy to również Przechwytywanie ruchu sieciowego na żądanie i inne zaawansowane funkcje.

Ten dokument zawiera szczegóły dotyczące obsługiwanych platform i opcje wdrażania dla rozszerzenia maszyny wirtualnej sieci obserwatorów agenta dla systemu Linux. Instalacja agenta nie zakłócać lub wymaga ponownego uruchomienia, maszyny wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie Agent monitora sieci mogą być uruchamiane na tych dystrybucje systemu Linux:

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

Należy pamiętać, że CoreOS nie jest obsługiwana w tej chwili.

### <a name="internet-connectivity"></a>Łączność z Internetem

Niektóre funkcje Agent monitora sieci wymaga, czy docelowa maszyna wirtualna jest połączony z Internetem. Bez możliwości nawiązywania połączeń wychodzących niektóre funkcje sieciowe obserwatorów agenta nieprawidłowe działanie lub stała się niedostępna. Aby uzyskać więcej informacji, zobacz [dokumentacji obserwatora sieciowego](/azure/network-watcher/).

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujące JSON zawiera schemat rozszerzenia Agent monitora sieci. Rozszerzenie nie wymaga nie obsługuje wszystkie ustawienia dostarczone przez użytkownika w tej chwili i zależy od konfiguracji domyślnej.

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

Rozszerzenia maszyny Wirtualnej platformy Azure można wdrożyć przy użyciu szablonów usługi Azure Resource Manager. Schematu JSON szczegółowo opisane w poprzedniej sekcji można w szablonie usługi Azure Resource Manager rozszerzenia sieci obserwatorów agenta są uruchamiane podczas wdrażania szablonu usługi Azure Resource Manager.

## <a name="azure-cli-deployment"></a>Wdrożenia usługi Azure CLI

Interfejsu wiersza polecenia Azure może służyć do wdrożenia rozszerzenia sieci obserwatorów agenta z maszyny Wirtualnej na istniejącej maszyny wirtualnej.

```azurecli
azure vm extension set myResourceGroup1 myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="troubleshooting-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożenia rozszerzenia może zostać pobrany z portalu Azure i przy użyciu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu wiersza polecenia platformy Azure.

```azurecli
azure vm extension get myResourceGroup1 myVM1
```

Dane wyjściowe wykonania rozszerzenia jest rejestrowany pliki znajdujące się w następującym katalogu:

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, można zapoznać się z dokumentacją obserwatora sieciowego lub skontaktuj się z ekspertami Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z platformy Azure obsługuje, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
