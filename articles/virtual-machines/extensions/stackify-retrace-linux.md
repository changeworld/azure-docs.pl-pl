---
title: Stackify odtwarzanie rozszerzenie Azure agenta systemu Linux | Dokumentacja firmy Microsoft
description: Wdróż agenta Stackify odtwarzanie systemu Linux na maszynie wirtualnej systemu Linux.
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: danis
ms.openlocfilehash: 376c5a087f74fbe087db9fa2df38b2ba4e6cf1ff
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify odtwarzanie rozszerzenia agenta systemu Linux

## <a name="overview"></a>Przegląd
Stackify zawiera produkty, które śledzą szczegółowe informacje o aplikacji ułatwia znajdowanie i szybkie rozwiązywanie problemów. Dla zespołów deweloperów odtwarzanie jest potęgą nadtypem wydajności aplikacji pełni zintegrowane, środowisko. Łączy kilka narzędzi potrzebuje co zespół deweloperów.

Odtwarzanie jest narzędzie tylko polegającego na dostarczaniu wszystkie następujące możliwości we wszystkich środowisk w pojedynczą platformę.

* Zarządzanie wydajnością aplikacji (APM)
* Rejestrowanie serwera i aplikacji
* Monitorowanie i śledzenie błędów
* Serwer aplikacji i metryki niestandardowe

**Około Stackify rozszerzenia agenta systemu Linux**

To rozszerzenie zapewnia ścieżki instalacji agenta systemu Linux dla odtwarzanie. 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny 
Agent odtwarzanie mogą być uruchamiane na tych dystrybucje systemu Linux

| Dystrybucja | Wersja |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16.10 i 17.04 |
| Debian | 7,9 + i 8.2 +, 9 |
| RedHat | 6.7 +, 7.1 + |
| CentOS | 6.3 +, 7.0 + |

### <a name="internet-connectivity"></a>Łączność z Internetem
Rozszerzenie Stackify agenta dla systemu Linux wymaga, aby docelowa maszyna wirtualna jest połączony z Internetem. 

Może być konieczne dostosowanie konfiguracji sieci, aby zezwalać na połączenia z Stackify, zobacz https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall. 


## <a name="extension-schema"></a>Schemat rozszerzenia
---

Następujące JSON zawiera schematu dla rozszerzenia Stackify odtwarzanie agenta. Wymaga rozszerzenia `environment` i `activationKey`.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu 

Rozszerzenia maszyny Wirtualnej platformy Azure można wdrożyć przy użyciu szablonów usługi Azure Resource Manager. Schematu JSON szczegółowo opisane w poprzedniej sekcji można w szablonie usługi Azure Resource Manager rozszerzenia Stackify odtwarzanie agenta systemu Linux są uruchamiane podczas wdrażania szablonu usługi Azure Resource Manager.  

JSON dla rozszerzenia maszyny wirtualnej mogą być zagnieżdżone wewnątrz zasobu maszyny wirtualnej lub umieszczony w katalogu głównego lub najwyższego poziomu szablonu usługi Resource Manager JSON. Umieszczanie JSON ma wpływ na wartość nazwy zasobów i typu. Aby uzyskać więcej informacji zobacz Nazwa zestawu i typ zasoby podrzędne.

Poniższy przykład przyjęto założenie, że rozszerzenie Stackify Linux odtwarzanie jest zagnieżdżona zasobu maszyny wirtualnej. Podczas zagnieżdżania rozszerzenia zasobu, JSON jest umieszczany w "zasoby": obiekt [] maszyny wirtualnej.

Wymaga rozszerzenia `environment` i `activationKey`.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

Podczas umieszczania rozszerzenia JSON w elemencie głównym szablonu, nazwy zasobu zawiera odwołanie do nadrzędnego maszyny wirtualnej, a typ odzwierciedla zagnieżdżonych.

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>Wdrożenie programu PowerShell

`Set-AzureRmVMExtension` Polecenia może służyć do wdrażania agenta systemu Linux odtwarzanie Stackify rozszerzenie maszyny wirtualnej na istniejącej maszyny wirtualnej. Przed uruchomieniem polecenia, konfiguracje publicznymi i prywatnymi muszą być przechowywane w tablicy skrótów programu PowerShell.

Wymaga rozszerzenia `environment` i `activationKey`.

```
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzureRmVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Wdrożenia usługi Azure CLI 

Narzędzie wiersza polecenia platformy Azure może służyć do wdrażania agenta systemu Linux odtwarzanie Stackify rozszerzenie maszyny wirtualnej na istniejącej maszyny wirtualnej.  

Wymaga rozszerzenia `environment` i `activationKey`.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i obsługa techniczna

### <a name="error-codes"></a>Kody błędów

| Kod błędu | Znaczenie | Możliwe działania |
| :---: | --- | --- |
| 10 | Błąd instalacji | wget jest wymagana |
| 20 | Błąd instalacji | Python jest wymagana |
| 30 | Błąd instalacji | sudo jest wymagana |
| 40 | Błąd instalacji | activationKey jest wymagana |
| 51 | Błąd instalacji | Distro systemu operacyjnego nie jest obsługiwane |
| 60 | Błąd instalacji | środowisko jest wymagana |
| 70 | Błąd instalacji | Nieznane |
| 80 | Włącz błędów | Ustawienia usługi nie powiodło się |
| 90 | Włącz błędów | Uruchamianie usługi nie powiodło się |
| 100 | Wyłącz błąd | Zatrzymywanie usługi nie powiodło się |
| 110 | Wyłącz błąd | Nie można usunąć usługi |
| 120 | Błąd odinstalowywania | Zatrzymywanie usługi nie powiodło się |

Jeśli potrzebujesz więcej pomocy można się z pomocą techniczną Stackify na https://support.stackify.com.