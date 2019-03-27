---
title: Azure szyfrowania dysku i Azure virtual machine scale sets z sekwencjonowania rozszerzeń
description: Ten artykuł zawiera instrukcje na temat włączania systemu Microsoft Azure Disk Encryption dla maszyn wirtualnych IaaS z systemem Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/21/2019
ms.openlocfilehash: e98e501806971f3cf1bec29960ad15ef9c0024fc
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498149"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Użyj usługi Azure Disk Encryption przy użyciu skali maszyny wirtualnej Ustaw sekwencjonowania rozszerzeń

Do skalowania maszyn wirtualnych platformy Azure można dodać rozszerzenia, takie jak usługa Azure disk encryption w określonej kolejności. Aby to zrobić, należy użyć [sekwencjonowania rozszerzeń](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). 

Ogólnie rzecz biorąc szyfrowania powinien być stosowany na dysku:

- Po rozszerzenia lub skryptów niestandardowych, który przygotowuje dyski lub woluminy.
- Przed przystąpieniem do rozszerzenia lub niestandardowe skrypty, które dostępu lub korzystają z danych na zaszyfrowane dyski lub woluminy.

W obu przypadkach `provisionAfterExtensions` właściwość wskazuje, której rozszerzenie powinny zostać dodane później w sekwencji.

## <a name="sample-azure-templates"></a>Przykładowe szablony usługi Azure

Jeśli chcesz użyć usługi Azure Disk Encryption zastosowane po innego rozszerzenia, umieść `provisionAfterExtensions` właściwości w bloku AzureDiskEncryption rozszerzenia. 

Oto przykład korzystający z "Rozszerzenie CustomScriptExtension", skryptu programu Powershell, który jest inicjowany i formatuje dysk Windows, a następnie "AzureDiskEncryption":

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

Jeśli chcesz użyć usługi Azure Disk Encryption stosowane przed innego rozszerzenia, umieść `provisionAfterExtensions` właściwości w bloku rozszerzenie do wykonania.

Oto przykład korzystający z "AzureDiskEncryption", po której następuje "VMDiagnosticsSettings", rozszerzenia, które oferuje funkcje monitorowania i diagnostyki możliwości maszyny wirtualnej platformy Azure Windows:


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

Aby uzyskać dodatkowe szczegółowe szablony Zobacz:
* Zastosowanie rozszerzenia usługi Azure Disk Encryption po skrypt powłoki niestandardowego, który sformatuje dysk (Linux): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)
* Zastosowanie rozszerzenia usługi Azure Disk Encryption po niestandardowego skryptu programu Powershell, który jest inicjowany i sformatuje dysk (Windows): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-ADE-after-customscript.json)
* Zastosowanie rozszerzenia usługi Azure Disk Encryption przed niestandardowego skryptu programu Powershell, który jest inicjowany i sformatuje dysk (Windows): [deploy-extseq-windows-CustomScript-after-ADE.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-CustomScript-after-ADE.json)

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej na temat sekwencjonowania rozszerzeń: [Sekwencja aprowizacja rozszerzenia w zestawach skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md).
- Dowiedz się więcej o `provisionAfterExtensions` właściwości: [Dokumentacja dotycząca Microsoft.Compute virtualMachineScaleSets/rozszerzenia szablonów](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
