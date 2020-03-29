---
title: Korzystanie z konfiguracji żądanego stanu z zestawami skalowania maszyny wirtualnej
description: Używanie zestawów skalowania maszyny wirtualnej z rozszerzeniem konfiguracji żądanego stanu platformy Azure do konfigurowania maszyn wirtualnych.
author: zjalexander
tags: azure-service-management,azure-resource-manager
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: a93a8a9c27be5a1736a50e6c4c4b830980b7d974
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278069"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Używanie zestawów skalowania maszyny wirtualnej z rozszerzeniem usługi Azure DSC
[Zestawy skalowania maszyny wirtualnej](virtual-machine-scale-sets-overview.md) mogą być używane z programem obsługi rozszerzenia [konfiguracji żądanego stanu platformy Azure (DSC).](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Zestawy skalowania maszyny wirtualnej umożliwiają wdrażanie dużej liczby maszyn wirtualnych i zarządzanie nimi oraz elastyczne skalowanie w odpowiedzi na obciążenie. DSC służy do konfigurowania maszyn wirtualnych, gdy są one w trybie online, więc są one uruchomione oprogramowanie produkcyjne.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Różnice między wdrażaniem w zestawach skalowania maszyn wirtualnych i maszyn wirtualnych
Podstawowa struktura szablonu dla zestawu skalowania maszyny wirtualnej różni się nieznacznie od pojedynczej maszyny wirtualnej. W szczególności pojedyncza maszyna wirtualna wdraża rozszerzenia w węźle "virtualMachines". Istnieje wpis typu "rozszerzenia", w którym DSC jest dodawany do szablonu

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

Węzeł zestawu skalowania maszyny wirtualnej ma sekcję "właściwości" z atrybutem "VirtualMachineProfile", "extensionProfile". DSC jest dodawany w obszarze "rozszerzenia"

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

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Zachowanie zestawu skalowania maszyny wirtualnej
Zachowanie zestawu skalowania maszyny wirtualnej jest identyczne z zachowaniem dla pojedynczej maszyny wirtualnej. Po utworzeniu nowej maszyny Wirtualnej jest automatycznie aprowizowana z rozszerzeniem DSC. Jeśli nowsza wersja WMF jest wymagana przez rozszerzenie, maszyna wirtualna uruchamia się ponownie przed przyjściem do trybu online. Gdy jest w trybie online, pobiera konfigurację DSC .zip i aprowizować go na maszynie Wirtualnej. Więcej szczegółów można znaleźć w [omówieniu rozszerzenia usługi Azure DSC](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Następne kroki
Sprawdź [szablon usługi Azure Resource Manager dla rozszerzenia DSC](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Dowiedz się, jak [rozszerzenie DSC bezpiecznie obsługuje poświadczenia.](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 

Aby uzyskać więcej informacji na temat programu obsługi rozszerzeń usługi Azure DSC, zobacz [Wprowadzenie do programu obsługi rozszerzenia konfiguracji żądanego stanu platformy Azure.](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 

Aby uzyskać więcej informacji na temat programu PowerShell DSC, [odwiedź centrum dokumentacji programu PowerShell](/powershell/scripting/dsc/overview/overview). 

