---
title: Stackify Retrace Azure Linux Agent Extension
description: Wdrażanie agenta Stackify Retrace Linux na maszynie wirtualnej systemu Linux.
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: akjosh
ms.openlocfilehash: 5914947bd994ee405f253e34c3dd919dd6561898
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253795"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify Retrace Rozszerzenie agenta Linuksa

## <a name="overview"></a>Omówienie

Stackify udostępnia produkty, które śledzą szczegóły dotyczące aplikacji, aby szybko znaleźć i rozwiązać problemy. Dla zespołów programistów Retrace to w pełni zintegrowana, wielozadaniowa, supermoce wydajności aplikacji. Łączy w sobie kilka narzędzi, które potrzebuje każdy zespół programistów.

Retrace to jedyne narzędzie, które zapewnia wszystkie następujące możliwości we wszystkich środowiskach na jednej platformie.

* Zarządzanie wydajnością aplikacji (APM)
* Rejestrowanie aplikacji i serwerów
* Śledzenie i monitorowanie błędów
* Metryki serwera, aplikacji i niestandardowych

**Informacje o rozszerzeniu programu Stackify Linux Agent Extension**

To rozszerzenie zapewnia ścieżkę instalacji dla agenta systemu Linux dla retrace. 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny 

Agent Retrace można uruchomić z tymi dystrybucjami Linuksa

| Dystrybucja | Wersja |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16.10 i 17.04 |
| Debian | 7,9+ i 8,2+, 9 |
| Red Hat | 6.7+, 7.1+ |
| CentOS | 6.3+, 7.0+ |

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie Stackify Agent dla systemu Linux wymaga, aby docelowa maszyna wirtualna była połączona z Internetem. 

Może być konieczne dostosowanie konfiguracji sieci, aby umożliwić https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewallpołączenia stackify, patrz . 


## <a name="extension-schema"></a>Schemat rozszerzenia

---

Poniżej przedstawiono schemat rozszerzenia stackify retrace agenta. Rozszerzenie wymaga `environment` i `activationKey`.

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

Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonów usługi Azure Resource Manager. Schemat JSON wyszczególniony w poprzedniej sekcji może służyć w szablonie usługi Azure Resource Manager do uruchamiania rozszerzenia Stackify Retrace Linux Agent podczas wdrażania szablonu usługi Azure Resource Manager.  

JSON dla rozszerzenia maszyny wirtualnej może być zagnieżdżony wewnątrz zasobu maszyny wirtualnej lub umieszczone na głównym lub najwyższym poziomie szablonu JSON Menedżera zasobów. Położenie JSON wpływa na wartość nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz Ustawianie nazwy i typu zasobów podrzędnych.

W poniższym przykładzie przyjęto założenie, że rozszerzenie Stackify Retrace Linux jest zagnieżdżone wewnątrz zasobu maszyny wirtualnej. Podczas zagnieżdżania zasobu rozszerzenia JSON jest umieszczany w "resources": [] obiekt maszyny wirtualnej.

Rozszerzenie wymaga `environment` i `activationKey`.

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

Podczas umieszczania rozszerzenia JSON w katalogu głównym szablonu nazwa zasobu zawiera odwołanie do nadrzędnej maszyny wirtualnej, a typ odzwierciedla konfigurację zagnieżdżoną.

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

Polecenie `Set-AzVMExtension` może służyć do wdrażania stackify Retrace Linux Agent rozszerzenie maszyny wirtualnej do istniejącej maszyny wirtualnej. Przed uruchomieniem polecenia konfiguracje publiczne i prywatne muszą być przechowywane w tabeli mieszania programu PowerShell.

Rozszerzenie wymaga `environment` i `activationKey`.

```powershell
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure 

Narzędzie interfejsu wiersza polecenia platformy Azure może służyć do wdrażania rozszerzenia maszyny wirtualnej Stackify Retrace Agent systemu Linux na istniejącej maszynie wirtualnej.  

Rozszerzenie wymaga `environment` i `activationKey`.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="error-codes"></a>Kody błędów

| Kod błędu | Znaczenie | Możliwe działanie |
| :---: | --- | --- |
| 10 | Błąd instalacji | wget jest wymagane |
| 20 | Błąd instalacji | python jest wymagany |
| 30 | Błąd instalacji | sudo jest wymagane |
| 40 | Błąd instalacji | activationKey jest wymagany |
| 51 | Błąd instalacji | Dystrybucja systemu operacyjnego nie jest obsługiwana |
| 60 | Błąd instalacji | wymagane jest środowisko |
| 70 | Błąd instalacji | Nieznane |
| 80 | Włącz błąd | Instalacja usługi nie powiodła się |
| 90 | Włącz błąd | Uruchomienie usługi nie powiodło się |
| 100 | Błąd wyłączania | Zatrzymanie usługi nie powiodło się |
| 110 | Błąd wyłączania | Usunięcie usługi nie powiodło się |
| 120 | Błąd odinstalowywania | Zatrzymanie usługi nie powiodło się |

Jeśli potrzebujesz więcej pomocy, możesz skontaktować się z pomocą techniczną Stackify pod adresem https://support.stackify.com.