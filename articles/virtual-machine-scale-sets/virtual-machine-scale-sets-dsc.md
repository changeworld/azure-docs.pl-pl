---
title: Za pomocą Desired State Configuration z zestawami skalowania maszyn wirtualnych | Dokumentacja firmy Microsoft
description: Inicjowanie zestawów za pomocą skalowania maszyn wirtualnych przy użyciu rozszerzenia Azure DSC
services: virtual-machine-scale-sets
documentationcenter: ''
author: zjalexander
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
keywords: ''
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: 24a37d352413ff9ac55ce8e189691988383950f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64728452"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Inicjowanie zestawów za pomocą skalowania maszyn wirtualnych przy użyciu rozszerzenia Azure DSC
[Virtual Machine Scale Sets](virtual-machine-scale-sets-overview.md) mogą być używane z [Azure Desired State Configuration (DSC)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) rozszerzenie obsługi. Zestawy skalowania maszyn wirtualnych umożliwiają wdrażanie i zarządzanie nimi dużej liczby maszyn wirtualnych i danych mogą elastycznie skalować w poziomie i pionie w odpowiedzi do załadowania. DSC jest używany do konfigurowania maszyn wirtualnych w miarę ich do trybu online, dzięki czemu są one uruchomione oprogramowania w środowiskach produkcyjnych.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Różnice między wdrażaniem maszyn wirtualnych i zestawów skalowania maszyn wirtualnych
Wewnętrzna struktura szablonu dla zestawu skalowania maszyn wirtualnych jest nieco różne od pojedynczej maszyny Wirtualnej. W szczególności pojedyncza maszyna wirtualna wdrażają rozszerzenia w węźle "maszyn wirtualnych". Istnieje wpis typu "rozszerzenia", w którym DSC jest dodawany do szablonu

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Węzeł zestaw skali maszyny wirtualnej zawiera sekcję "properties" z "VirtualMachineProfile", "extensionProfile" atrybutu. DSC jest dodawany w częściach "rozszerzenia"

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Zachowanie dla zestawu skalowania maszyn wirtualnych
Zachowanie dla zestawu skalowania maszyn wirtualnych jest taka sama jak zachowanie dla pojedynczej maszyny Wirtualnej. Po utworzeniu nowej maszyny Wirtualnej zostanie automatycznie aprowizowana za pomocą rozszerzenia DSC. Jeśli nowsza wersja WMF jest wymagane przez rozszerzenie, przed położonym ponowny rozruch maszyny Wirtualnej. Gdy jest w trybie online, pliki do pobrania zip konfiguracji DSC i aprowizować go na maszynie Wirtualnej. Więcej szczegółów można znaleźć w [Omówienie rozszerzenia DSC usługi Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Kolejne kroki
Sprawdź [szablonu usługi Azure Resource Manager dla rozszerzenia DSC](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Dowiedz się, jak [rozszerzenia DSC bezpieczną obsługę poświadczeń](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Aby uzyskać więcej informacji na temat obsługi rozszerzenia DSC usługi Azure, zobacz [wprowadzenie do procedury obsługi rozszerzenia Azure Desired State Configuration](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Aby uzyskać więcej informacji na temat programu PowerShell DSC [odwiedź Centrum dokumentacji programu PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

