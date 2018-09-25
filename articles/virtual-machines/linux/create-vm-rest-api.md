---
title: Utwórz maszynę wirtualną systemu Linux przy użyciu interfejsu API REST platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć maszynę wirtualną z systemem Linux na platformie Azure, która korzysta z uwierzytelniania Managed Disks i ustawieniami SSH przy użyciu interfejsu API REST platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 11d9f5efb452d46e5ca30169861582f6f2bbbd1b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969397"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Utwórz maszynę wirtualną systemu Linux, która korzysta z uwierzytelniania SSH przy użyciu interfejsu API REST

Maszynę wirtualną (VM) na platformie Azure jest definiowany przez różnych parametrów, takich jak lokalizacja, rozmiar sprzętu, obraz systemu operacyjnego i poświadczenia logowania. W tym artykule pokazano, jak używać interfejsu API REST do utworzenia maszyny wirtualnej systemu Linux, która korzysta z uwierzytelniania SSH.

Aby utworzyć lub zaktualizować maszyny wirtualnej, należy użyć następującego *umieścić* operacji:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

## <a name="create-a-request"></a>Utwórz żądanie

Aby utworzyć *umieścić* żądania `{subscription-id}` parametr jest wymagany. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). Należy zdefiniować `{resourceGroupName}` i `{vmName}` dla zasobów, wraz z `api-version` parametru. W tym artykule wykorzystano `api-version=2017-12-01`.

Wymagane są następujące nagłówki:

| Nagłówek żądania   | Opis |
|------------------|-----------------|
| *Typ zawartości:*  | Wymagany. Ustaw `application/json`. |
| *Autoryzacja:* | Wymagany. Ustawić prawidłową `Bearer` [token dostępu](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Aby uzyskać więcej informacji o tym, jak utworzyć żądanie, zobacz [składniki żądania/odpowiedzi interfejsu API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Tworzenie treści żądania

Następujące typowe definicje są używane do tworzenia treści żądania:

| Name (Nazwa)                       | Wymagane | Typ                                                                                | Opis  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | ciąg                                                                              | Lokalizacja zasobu. |
| name                       |          | ciąg                                                                              | Nazwa maszyny wirtualnej. |
| properties.hardwareProfile |          | [— Obiekt HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Określa ustawienia sprzętu dla maszyny wirtualnej. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Określa ustawienia przechowywania dla dysków maszyny wirtualnej. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Określa ustawienia systemu operacyjnego dla maszyny wirtualnej. |
| properties.networkProfile  |          | [Elementy NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Określa interfejsy sieciowe maszyny wirtualnej. |

Aby uzyskać pełną listę dostępnych definicji w treści żądania, zobacz [maszyn wirtualnych, Utwórz lub zaktualizuj definicje treść żądania](/rest/api/compute/virtualmachines/createorupdate#definitions).

### <a name="example-request-body"></a>Przykład treść żądania

Treść żądania poniższy przykład definiuje obrazu systemu Ubuntu 18.04 LTS, który używa dysków zarządzanych w warstwie Premium. Uwierzytelnianie klucza publicznego SSH jest używany, a maszyna wirtualna używa istniejącej wirtualnej karty sieciowej (NIC) czy masz [utworzone wcześniej](../../virtual-network/virtual-network-network-interface.md). Podaj klucz publiczny SSH w *osProfile.linuxConfiguration.ssh.publicKeys.keyData* pola. Jeśli to konieczne, możesz [generowania pary kluczy SSH](mac-create-ssh-keys.md).

```json
{
  "location": "eastus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
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
  },
  "name": "myVM"
}
```

## <a name="responses"></a>Odpowiedzi

Istnieją dwa pomyślne odpowiedzi dla operacji, które można utworzyć lub zaktualizować maszyny wirtualnej:

| Name (Nazwa)        | Typ                                                                              | Opis |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 utworzono | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Utworzone     |

Aby uzyskać więcej informacji na temat odpowiedzi interfejsu API REST, zobacz [przetworzyć komunikatu odpowiedzi](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Przykładowa odpowiedź

Skrócone *201 utworzono* odpowiedź z treści żądania przykład poprzedniej, która umożliwia utworzenie maszyny Wirtualnej wskazuje *vmId* przypisano i *provisioningState* jest *Tworzenie*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsów API REST platformy Azure lub narzędzi do zarządzania, takich jak wiersza polecenia platformy Azure lub programu Azure PowerShell zobacz następujące tematy:

- [Dostawca usługi Azure Compute interfejsu API REST](/rest/api/compute/)
- [Wprowadzenie do interfejsu API REST platformy Azure](/rest/api/azure/)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/)
- [Moduł programu Azure PowerShell](/powershell/azure/overview)
