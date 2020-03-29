---
title: Sekwencjowanie rozszerzeń zestawów rozszerzeń usługi Azure Disk Encryption i Azure virtual machine
description: Ten artykuł zawiera instrukcje dotyczące włączania szyfrowania dysków platformy Microsoft Azure dla maszyn wirtualnych IaaS systemu Linux.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.openlocfilehash: aa638b86b0788b8c274f9dcb3c04c1fc385b4ae1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279027"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Używanie szyfrowania dysku platformy Azure z sekwencjonowaniem rozszerzenia zestawu skalowania maszyny wirtualnej

Rozszerzenia, takie jak szyfrowanie dysków platformy Azure można dodać do zestawu maszyn wirtualnych platformy Azure w określonej kolejności. Aby to zrobić, należy użyć [sekwencjonowania rozszerzeń](virtual-machine-scale-sets-extension-sequencing.md). 

Ogólnie rzecz biorąc, szyfrowanie powinno być stosowane do dysku:

- Po rozszerzeniach lub skryptach niestandardowych, które przygotowują dyski lub woluminy.
- Przed rozszerzeniami lub skryptami niestandardowymi, które uzyskują dostęp do danych zaszyfrowanych lub woluminów lub zużywają z nich dane.

W obu przypadkach `provisionAfterExtensions` właściwość określa, które rozszerzenie należy dodać później w sekwencji.

## <a name="sample-azure-templates"></a>Przykładowe szablony platformy Azure

Jeśli chcesz, aby szyfrowanie dysków platformy Azure `provisionAfterExtensions` stosowane po innym rozszerzeń, umieścić właściwość w bloku rozszerzenia AzureDiskEncryption. 

Oto przykład użycia "CustomScriptExtension", skryptu programu Powershell, który inicjuje i formatuje dysk systemu Windows, a następnie "AzureDiskEncryption":

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

Jeśli chcesz mieć azure szyfrowania dysku stosowane przed `provisionAfterExtensions` innym rozszerzeniem, umieść właściwość w bloku rozszerzenia do naśladowania.

Oto przykład przy użyciu "AzureDiskEncryption", a następnie "VMDiagnosticsSettings", rozszerzenie, które zapewnia funkcje monitorowania i diagnostyki na maszynie Wirtualnej platformy Azure opartej na systemie Windows:


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

Aby uzyskać bardziej szczegółowy szablon, zobacz:
* Stosowanie rozszerzenia szyfrowania dysków platformy Azure po niestandardowych skryptach powłoki, który formatuje dysk (Linux): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o sekwencjonowaniu rozszerzeń: [inicjowanie obsługi administracyjnej rozszerzenia sekwencji w zestawach skalowania maszyny wirtualnej](virtual-machine-scale-sets-extension-sequencing.md).
- Dowiedz się `provisionAfterExtensions` więcej o właściwości: [Microsoft.Compute virtualMachineScaleSets/extensions template reference](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Szyfrowanie dysków platformy Azure dla zestawów skalowania maszyn wirtualnych](disk-encryption-overview.md)
- [Szyfruj zestawy skalowania maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli.md)
- [Szyfrowanie zestawów skalowania maszyny wirtualnej przy użyciu programu Azure PowerShell](disk-encryption-powershell.md)
- [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure](disk-encryption-key-vault.md)
