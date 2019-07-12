---
title: Stackify odtwarzanie rozszerzenia agenta systemu Linux platformy Azure | Dokumentacja firmy Microsoft
description: Wdróż Stackify odtwarzanie agenta systemu Linux na maszynie wirtualnej systemu Linux.
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: roiyz
ms.openlocfilehash: ca3232dc3862cffb67d396b3ec2333fdc28dbb65
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706638"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify odtwarzanie rozszerzenia agenta systemu Linux

## <a name="overview"></a>Omówienie

Stackify zawiera produkty, które śledzą szczegółowe informacje o aplikacji w taki sposób, aby ułatwić znajdowanie i szybkie rozwiązywanie problemów. Dla zespołów deweloperów odtwarzanie jest wydajność administratorami w pełni zintegrowanego środowiska wielu aplikacji. Łączy kilka narzędzi, czego potrzebuje każdy zespół programistów.

Odtwarzanie to narzędzie tylko, która udostępnia wszystkie następujące funkcje we wszystkich środowiskach w ramach jednej platformy.

* Zarządzanie wydajnością aplikacji (APM)
* Rejestrowanie serwera i aplikacji
* Monitorowanie i śledzenie błędów
* Serwer, aplikacji i metryki niestandardowe

**Około Stackify rozszerzenia agenta systemu Linux**

To rozszerzenie zapewnia ścieżkę instalacji dla agenta systemu Linux dla odtwarzanie. 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny 

Odtwarzanie agenta mogą być uruchamiane względem tych dystrybucje systemu Linux

| Dystrybucja | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS 16.10 i 17.04 |
| Debian | 7,9 + i 8.2 +, 9 |
| Red Hat | 6.7+, 7.1+ |
| CentOS | 6.3+, 7.0+ |

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie Stackify agenta dla systemu Linux wymaga, że docelowej maszyny wirtualnej jest połączony z Internetem. 

Może być konieczne dostosowanie konfigurację sieci, aby zezwolić na połączenia z rozwiązania Stackify, zobacz https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall. 


## <a name="extension-schema"></a>Schemat rozszerzenia

---

Następujący kod JSON zawiera schemat dla rozszerzenia Stackify odtwarzanie agenta. Rozszerzenie wymaga `environment` i `activationKey`.

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

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Schemat JSON szczegółowo opisane w poprzedniej sekcji może służyć w szablonie usługi Azure Resource Manager do uruchomienia rozszerzenia Stackify odtwarzanie agenta systemu Linux podczas wdrażania szablonu usługi Azure Resource Manager.  

Kod JSON dla rozszerzenia maszyny wirtualnej mogą być zagnieżdżone wewnątrz zasobu maszyny wirtualnej lub umieszczone w katalogu głównego lub najwyższego poziomu szablon JSON usługi Resource Manager. Rozmieszczanie za pomocą pliku JSON ma wpływ na wartości nazwy i typu zasobu. Aby uzyskać więcej informacji zobacz Nazwa zestawu i typu dla zasobów podrzędnych.

W poniższym przykładzie założono, że rozszerzenia Stackify odtwarzanie systemu Linux jest zagnieżdżona w obrębie zasobu maszyny wirtualnej. Podczas zagnieżdżania rozszerzenia zasobu, za pomocą pliku JSON jest umieszczane w "zasoby": obiekt [] maszyny wirtualnej.

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

Podczas umieszczania rozszerzenia JSON w katalogu głównym szablonu, nazwa zasobu zawiera odwołanie do nadrzędnej maszyny wirtualnej, a typ odzwierciedla zagnieżdżonych.

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

`Set-AzVMExtension` Polecenie może służyć do wdrażania agenta systemu Linux odtwarzanie Stackify rozszerzenie maszyny wirtualnej na istniejącej maszyny wirtualnej. Przed uruchomieniem polecenia, konfiguracje publicznymi i prywatnymi muszą być przechowywane w tabeli wyznaczania wartości skrótu programu PowerShell.

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

## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure 

Narzędzie wiersza polecenia platformy Azure może służyć do wdrażania agenta systemu Linux odtwarzanie Stackify rozszerzenie maszyny wirtualnej na istniejącej maszyny wirtualnej.  

Rozszerzenie wymaga `environment` i `activationKey`.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="error-codes"></a>Kody błędów

| Kod błędu | Znaczenie | Możliwe działania |
| :---: | --- | --- |
| 10 | Błąd instalacji | wymagana jest wget |
| 20 | Błąd instalacji | język Python jest wymagana |
| 30 | Błąd instalacji | "sudo" jest wymagana |
| 40 | Błąd instalacji | klucz activationKey jest wymagana |
| 51 | Błąd instalacji | Dystrybucja systemu operacyjnego nie jest obsługiwane |
| 60 | Błąd instalacji | środowisko jest wymagane |
| 70 | Błąd instalacji | Nieznane |
| 80 | Włącz błędów | Ustawienia usługi nie powiodło się |
| 90 | Włącz błędów | Uruchamianie usługi nie powiodło się |
| 100 | Wyłącz błąd | Zatrzymywanie usługi nie powiodło się |
| 110 | Wyłącz błąd | Usunięcie usługi nie powiodło się |
| 120 | Błąd dezinstalacji | Zatrzymywanie usługi nie powiodło się |

Jeśli potrzebujesz więcej pomocy możesz skontaktować się ze rozwiązania Stackify pomocy technicznej związanej z https://support.stackify.com.