---
title: Rozwiązywanie problemów z wdrażaniem maszyny wirtualnej z powodu dysków odłączonych | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z wdrażaniem maszyny wirtualnej z powodu dysków odłączonych
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2019
ms.author: vaaga
ms.openlocfilehash: e049a2b914cbf9c4f0ca0f3a1dd0281d58f881b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75486822"
---
# <a name="troubleshoot-virtual-machine-deployment-due-to-detached-disks"></a>Rozwiązywanie problemów z wdrażaniem maszyny wirtualnej z powodu dysków odłączonych

## <a name="symptom"></a>Objaw

Podczas próby aktualizacji maszyny wirtualnej, której poprzedni dysk danych odłączył się nie powiodło się, można natknąć się na ten kod błędu.

```
Code=\"AttachDiskWhileBeingDetached\" 
Message=\"Cannot attach data disk '{disk ID}' to virtual machine '{vmName}' because the disk is currently being detached or the last detach  operation failed. Please wait until the disk is completely detached, and then try again or delete/detach the disk explicitly again\” 
```

## <a name="cause"></a>Przyczyna

Ten błąd występuje podczas próby ponownego dołączenia dysku danych, którego ostatnia operacja odłączania nie powiodła się. Najlepszym sposobem, aby wydostać się z tego stanu jest odłączenie dysku wada.

## <a name="solution-1-powershell"></a>Rozwiązanie 1: Powershell

### <a name="step-1-get-the-virtual-machine-and-disk-details"></a>Krok 1: Pobierz szczegóły maszyny wirtualnej i dysku

```azurepowershell-interactive
PS D:> $vm = Get-AzureRmVM -ResourceGroupName "Example Resource Group" -Name "ERGVM999999" 

PS D:> $vm.StorageProfile.DataDisks 
lun          : 0
name         : f94901ef-75ee-4477-9ad6-1c74da50e7ef
createOption : Attach
caching      : ReadOnly
managedDisk  : @{storageAccountType=Premium_LRS; id=/subscriptions/<subscription ID>/resourceGroups/<Resource Group name>}
diskSizeGB   : 8
toBeDetached : False 
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>Krok 2: Ustaw flagę dla dysków wadających na "true".

Pobierz indeks tablicy uszkodzonego dysku i ustaw flagę **toBeDetached** dla uszkodzonego dysku (dla którego wystąpił błąd **AttachDiskWhileBeingDetached)** na "true").Get the array index of the failing disk and set the toBeDetached flag for the failing disk (for which AttachDiskWhileBeingDetached error occurred) to "true". To ustawienie oznacza odłączenie dysku od maszyny wirtualnej. Uszkodzoną nazwę dysku można znaleźć w **errorMessage**.

> ! Uwaga: Wersja interfejsu API określona dla wywołań Get i Put musi być 2019-03-01 lub większa.

```azurepowershell-interactive
PS D:> $vm.StorageProfile.DataDisks[0].ToBeDetached = $true 
```

Alternatywnie można również odłączyć ten dysk za pomocą poniższego polecenia, co będzie pomocne dla użytkowników korzystających z wersji interfejsu API przed 1 marca 2019.

```azurepowershell-interactive
PS D:> Remove-AzureRmVMDataDisk -VM $vm -Name "<disk ID>" 
```

### <a name="step-3-update-the-virtual-machine"></a>Krok 3: Aktualizacja maszyny wirtualnej

```azurepowershell-interactive
PS D:> Update-AzureRmVM -ResourceGroupName "Example Resource Group" -VM $vm 
```

## <a name="solution-2-rest"></a>Rozwiązanie 2: REST

### <a name="step-1-get-the-virtual-machine-payload"></a>Krok 1: Uzyskaj ładunek maszyny wirtualnej.

```azurepowershell-interactive
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?$expand=instanceView&api-version=2019-03-01
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>Krok 2: Ustaw flagę dla dysków wadających na "true".

Ustaw **flagę toBeDetached** dla dysku wada true w ładunku zwrócony w kroku 1. Uwaga: Wersja interfejsu API określona dla wywołań Get and Put musi być `2019-03-01` większa.

**Treść żądania przykładu**

```azurepowershell-interactive
{
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D2_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      },
      "dataDisks": [
        {
          "diskSizeGB": 1023,
          "createOption": "Empty",
        "name": "f94901ef-75ee-4477-9ad6-1c74da50e7ef",
          "lun": 0,
          "toBeDetached": true
        },
        {
          "diskSizeGB": 1023,
          "createOption": "Empty",
          "lun": 1,
          "toBeDetached": false
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

Alternatywnie można również usunąć dysk danych, który nie działa z powyższego ładunku, co jest przydatne dla użytkowników korzystających z wersji interfejsu API przed 01 marca 2019.

### <a name="step-3-update-the-virtual-machine"></a>Krok 3: Aktualizacja maszyny wirtualnej

Użyj ładunku treści żądania ustawionego w kroku 2 i zaktualizuj maszynę wirtualną w następujący sposób:

```azurepowershell-interactive
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2019-03-01
```

**Przykładowa odpowiedź:**

```azurepowershell-interactive
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "type": "Microsoft.Compute/virtualMachines",
  "properties": {
    "osProfile": {
      "adminUsername": "{your-username}",
      "secrets": [],
      "computerName": "myVM",
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/nsgExistingNic",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "osType": "Windows",
        "caching": "ReadWrite",
        "createOption": "FromImage",
        "name": "myVMosdisk",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        }
      },
      "dataDisks": [
        {
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "createOption": "Empty",
          "lun": 0,
          "diskSizeGB": 1023,
        "name": "f94901ef-75ee-4477-9ad6-1c74da50e7ef",
          "toBeDetached": true
        },
        {
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "createOption": "Empty",
          "lun": 1,
          "diskSizeGB": 1023,
          "toBeDetached": false
        }
      ]
    },
    "vmId": "3906fef9-a1e5-4b83-a8a8-540858b41df0",
    "hardwareProfile": {
      "vmSize": "Standard_D2_v2"
    },
    "provisioningState": "Updating"
  },
  "name": "myVM",
  "location": "westus"
}
```

## <a name="next-steps"></a>Następne kroki

Jeśli masz problemy z połączeniem się z maszyną [wirtualną, zobacz Rozwiązywanie problemów z połączeniami protokołu RDP z maszyną wirtualną platformy Azure.](troubleshoot-rdp-connection.md)

Aby uzyskać problemy z uzyskiwaniem dostępu do aplikacji uruchomionych na maszynie wirtualnej, zobacz [Rozwiązywanie problemów z łącznością aplikacji na maszynie Wirtualnej systemu Windows](troubleshoot-app-connection.md).
