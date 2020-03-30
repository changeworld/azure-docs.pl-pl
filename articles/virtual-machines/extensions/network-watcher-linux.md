---
title: Rozszerzenie maszyny wirtualnej agenta obserwatora sieci azure dla systemu Linux
description: Wdrażanie agenta obserwatora sieci na maszynie wirtualnej systemu Linux przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-linux
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 712ec177996cd54d7bd6d184fea306009b58b083
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531026"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Rozszerzenie agenta usługi Network Watcher dla maszyny wirtualnej dla systemu Linux

## <a name="overview"></a>Omówienie

[Azure Network Watcher](/azure/network-watcher/) to usługa monitorowania wydajności sieci, diagnostyki i analizy, która umożliwia monitorowanie sieci platformy Azure. Rozszerzenie maszyny wirtualnej agenta obserwatora sieciowego jest wymagane dla niektórych funkcji obserwatora sieci na maszynach wirtualnych platformy Azure, takich jak przechwytywanie ruchu sieciowego na żądanie i innych zaawansowanych funkcji.

W tym artykule opisano obsługiwane platformy i opcje wdrażania rozszerzenia maszyny Wirtualnej agenta obserwatora sieci dla systemu Linux. Instalacja agenta nie zakłóca ani nie wymaga ponownego uruchomienia maszyny Wirtualnej. Rozszerzenie można wdrożyć na maszynach wirtualnych, które można wdrożyć. Jeśli maszyna wirtualna jest wdrażana przez usługę platformy Azure, sprawdź dokumentację usługi, aby ustalić, czy zezwala na instalowanie rozszerzeń na maszynie wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie Agenta obserwatora sieci można skonfigurować dla następujących dystrybucji systemu Linux:

| Dystrybucja | Wersja |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 i 8 |
| Red Hat | 6 i 7 |
| Oracle Linux | 6,8+ i 7 |
| SUSE Linux Enterprise Server | 11 i 12 |
| Skok OpenSUSE | 42,3+ |
| CentOS | 6,5+ i 7 |
| CoreOS | 899.17.0+ |


### <a name="internet-connectivity"></a>Łączność z Internetem

Niektóre funkcje agenta obserwatora sieci wymaga, aby maszyna wirtualna była połączona z Internetem. Bez możliwości nawiązyania połączeń wychodzących niektóre funkcje agenta obserwatora sieci mogą działać nieprawidłowo lub stać się niedostępne. Aby uzyskać więcej informacji na temat funkcji funkcji obserwatora sieci, która wymaga[agenta, zobacz dokumentację obserwatora sieci](/azure/network-watcher/).

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniżej przedstawiono schemat rozszerzenia agenta obserwatora sieci. Rozszerzenie nie wymaga ani nie obsługuje żadnych ustawień dostarczonych przez użytkownika. Rozszerzenie opiera się na jego domyślnej konfiguracji.

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

| Nazwa | Wartość / Przykład |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| wydawca | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentLinux (Obserwator sieciowy) |
| typHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonu usługi Azure Resource Manager. Aby wdrożyć rozszerzenie agenta obserwatora sieci, należy użyć poprzedniego schematu json w szablonie.

## <a name="azure-classic-cli-deployment"></a>Wdrożenie klasycznego interfejsu wiersza polecenia platformy Azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

W poniższym przykładzie wdraża rozszerzenie maszyny Wirtualnej agenta obserwatora sieciowego na istniejącej maszynie wirtualnej wdrożonej za pośrednictwem klasycznego modelu wdrażania:

```console
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

W poniższym przykładzie wdraża rozszerzenie maszyny Wirtualnej agenta obserwatora sieciowego na istniejącej maszynie wirtualnej wdrożonej za pośrednictwem Menedżera zasobów:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Można pobrać dane o stanie wdrożeń rozszerzeń przy użyciu witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.

W poniższym przykładzie przedstawiono stan wdrożenia rozszerzenia NetworkWatcherAgentLinux dla maszyny Wirtualnej wdrożonej za pośrednictwem Menedżera zasobów przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie tego artykułu, możesz zapoznać się z [dokumentacją obserwatora sieci](/azure/network-watcher/)lub skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**. Aby uzyskać informacje dotyczące korzystania z pomocy technicznej platformy Azure, zobacz [często zadawane pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).
