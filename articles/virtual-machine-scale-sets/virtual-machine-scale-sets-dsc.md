---
title: Przy użyciu Konfiguracja żądanego stanu z zestawy skalowania maszyny wirtualnej | Dokumentacja firmy Microsoft
description: Ustawia przy użyciu skali maszyny wirtualnej z platformy Azure rozszerzenia usługi Konfiguracja DSC
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
ms.openlocfilehash: a68a5f31952d636c054b66dc0bb6ec0579cd7192
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Ustawia przy użyciu skali maszyny wirtualnej z platformy Azure rozszerzenia usługi Konfiguracja DSC
[Zestawy skalowania maszyny wirtualnej](virtual-machine-scale-sets-overview.md) mogą być używane z [konfiguracji żądanego stanu Azure (DSC)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) rozszerzenia obsługi. Zestawy skalowania maszyny wirtualnej umożliwiają wdrażanie i zarządzanie nimi dużej liczby maszyn wirtualnych i elastycznie można skalować i zmniejszanie w odpowiedzi na obciążenia. DSC jest używany do konfigurowania maszyn wirtualnych po ich przejdzie w tryb online, są na nich uruchomione oprogramowanie produkcji.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Różnice między wdrożeniem maszyn wirtualnych i zestawy skalowania maszyny wirtualnej
Wewnętrzna struktura szablonu dla zestawu skalowania maszyny wirtualnej jest nieco inne niż jednej maszyny Wirtualnej. W szczególności jednej maszyny Wirtualnej wdraża rozszerzenia w węźle "virtualMachines". Brak wpisu typu "rozszerzenia", w którym DSC zostanie dodany do szablonu

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

Węzeł zestaw skali maszyny wirtualnej ma sekcję "właściwości" od "VirtualMachineProfile", "extensionProfile" atrybutu. DSC zostanie dodany w obszarze "rozszerzenia"

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

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Zachowanie dla zestawu skalowania maszyny wirtualnej
Zachowanie dla zestawu skalowania maszyny wirtualnej jest taki sam jak zachowanie dla jednej maszyny Wirtualnej. Podczas tworzenia nowej maszyny Wirtualnej, jest on automatycznie udostępniane z rozszerzenia usługi Konfiguracja DSC. Jeśli nowsza wersja programu WMF jest wymagane przez rozszerzenie, ponowne uruchomienie maszyny Wirtualnej przed przełączeniem do trybu online. Gdy jest w trybie online, pobiera .zip konfiguracji DSC i udostępnić go na maszynie Wirtualnej. Więcej informacji można znaleźć w [Omówienie rozszerzenia DSC Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Kolejne kroki
Sprawdź [szablonu usługi Azure Resource Manager dla rozszerzenia DSC](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Dowiedz się, jak [rozszerzenia DSC bezpieczną obsługę poświadczeń](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Aby uzyskać więcej informacji dotyczących obsługi rozszerzenia usługi Konfiguracja DSC Azure, zobacz [wprowadzenie do obsługi rozszerzenia konfiguracji żądanego stanu Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Aby uzyskać więcej informacji o konfiguracji DSC środowiska PowerShell [odwiedź Centrum dokumentacji programu PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

