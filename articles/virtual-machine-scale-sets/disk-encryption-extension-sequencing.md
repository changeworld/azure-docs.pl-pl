---
title: Sekwencjonowanie rozszerzeń zestawów skalowania maszyn wirtualnych Azure Disk Encryption i platformy Azure
description: Ten artykuł zawiera instrukcje na temat włączania systemu Microsoft Azure Disk Encryption dla maszyn wirtualnych IaaS z systemem Linux.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.openlocfilehash: aa638b86b0788b8c274f9dcb3c04c1fc385b4ae1
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279027"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Użyj Azure Disk Encryption z sekwencjonowaniem rozszerzenia zestawu skalowania maszyn wirtualnych

Rozszerzenia, takie jak Azure Disk Encryption, można dodać do zestawu skalowania maszyn wirtualnych platformy Azure w określonej kolejności. W tym celu należy użyć [sekwencjonowania rozszerzeń](virtual-machine-scale-sets-extension-sequencing.md). 

Ogólnie rzecz biorąc należy zastosować szyfrowanie do dysku:

- Po rozszerzeniu lub skryptach niestandardowych przygotowujących dyski lub woluminy.
- Przed rozszerzeniami lub skryptami niestandardowymi, które uzyskują dostęp lub zużywają dane na zaszyfrowanych dyskach lub woluminach.

W obu przypadkach Właściwość `provisionAfterExtensions` określa, które rozszerzenie należy dodać w dalszej części sekwencji.

## <a name="sample-azure-templates"></a>Przykładowe szablony platformy Azure

Jeśli chcesz, aby Azure Disk Encryption zastosowały się po innym rozszerzeniu, należy umieścić Właściwość `provisionAfterExtensions` w bloku rozszerzenia AzureDiskEncryption. 

Oto przykład użycia "CustomScriptExtension", skryptu programu PowerShell, który inicjuje i formatuje dysk systemu Windows, po którym następuje "AzureDiskEncryption":

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

Jeśli chcesz, aby Azure Disk Encryption zastosowały się przed innym rozszerzeniem, należy umieścić Właściwość `provisionAfterExtensions` w bloku rozszerzenia.

Oto przykład użycia "AzureDiskEncryption", po którym następuje "VMDiagnosticsSettings", rozszerzenia, które zapewnia możliwości monitorowania i diagnostyki na maszynie wirtualnej platformy Azure opartej na systemie Windows:


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

Aby zapoznać się z bardziej szczegółowym szablonem, zobacz:
* Zastosuj rozszerzenie Azure Disk Encryption po skrypcie powłoki niestandardowej, który sformatuje dysk (Linux): [Deploy-extseq-Linux-ADE-After-CustomScript. JSON](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat sekwencjonowania rozszerzeń: [Inicjowanie obsługi rozszerzeń sekwencji w zestawach skalowania maszyn wirtualnych](virtual-machine-scale-sets-extension-sequencing.md).
- Dowiedz się więcej o właściwości `provisionAfterExtensions`: [Microsoft. COMPUTE virtualMachineScaleSets/Extensions Template Reference](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Azure Disk Encryption dla zestawów skalowania maszyn wirtualnych](disk-encryption-overview.md)
- [Szyfrowanie zestawów skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli.md)
- [Szyfrowanie zestawów skalowania maszyn wirtualnych przy użyciu Azure PowerShell](disk-encryption-powershell.md)
- [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md)
