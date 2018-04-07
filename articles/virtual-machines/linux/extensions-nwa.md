---
title: Rozszerzenie maszyny wirtualnej Azure Agent obserwatorów sieci dla systemu Linux | Dokumentacja firmy Microsoft
description: Wdróż agenta monitora sieci na maszynie wirtualnej systemu Linux przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-linux
documentationcenter: ''
author: dennisg
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
ms.openlocfilehash: 487eca98b9be20faaa52c0a8952e84c6027ee1f0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Rozszerzenie maszyny wirtualnej obserwatorów agenta sieciowe dla systemu Linux

## <a name="overview"></a>Przegląd

[Azure obserwatora sieciowego](https://docs.microsoft.com/azure/network-watcher/) jest sieci performance monitoring, Diagnostyka i analiza usługa, która umożliwia monitorowanie sieci platformy Azure. Rozszerzenie maszyny wirtualnej sieci obserwatorów agenta jest wymagane dla niektórych funkcji obserwatora sieciowego na maszynach wirtualnych Azure. Dotyczy to również Przechwytywanie ruchu sieciowego na żądanie i inne zaawansowane funkcje.

Ten dokument zawiera szczegóły dotyczące obsługiwanych platform i opcje wdrażania dla rozszerzenia maszyny wirtualnej sieci obserwatorów agenta dla systemu Linux. Instalacja agenta nie zakłócać lub wymaga ponownego uruchomienia, maszyny wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie Agent monitora sieci mogą być uruchamiane na tych dystrybucje systemu Linux:

| Dystrybucja | Wersja |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS i 12.04 LTS |
| Debian | 7 i 8 |
| RedHat | 6.x i 7.x |
| Oracle Linux | 7x |
| SUSE | 11 i 12 |
| OpenSuse | 7.0 |
| CentOS | 7.0 |

Należy pamiętać, że CoreOS nie jest obsługiwana w tej chwili.

### <a name="internet-connectivity"></a>Łączność z Internetem

Niektóre funkcje Agent monitora sieci wymaga, czy docelowa maszyna wirtualna jest połączony z Internetem. Bez możliwości nawiązywania połączeń wychodzących niektóre funkcje sieciowe obserwatorów agenta nieprawidłowe działanie lub stała się niedostępna. Aby uzyskać więcej informacji, zobacz [dokumentacji obserwatora sieciowego](https://review.docs.microsoft.com/azure/network-watcher/).

## <a name="extension-schema"></a>Rozszerzenie schematu

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
| publisher | Microsoft.Azure.NetworkWatcher |
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

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, można zapoznać się z dokumentacją obserwatora sieciowego lub skontaktuj się z ekspertami Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/en-us/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/en-us/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z platformy Azure obsługuje, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/en-us/support/faq/).
