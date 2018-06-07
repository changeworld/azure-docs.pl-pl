---
title: Utwórz maszynę wirtualną systemu Linux przy użyciu interfejsu API REST Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia maszyny wirtualnej systemu Linux na platformie Azure korzystającej z uwierzytelniania zarządzane dyski i SSH z interfejsu API REST Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: e3f41bea26e9a5ff45b31ae9d9a2e5955317ad7a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825325"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Tworzenie maszyny wirtualnej systemu Linux, który korzysta z uwierzytelniania SSH przy użyciu interfejsu API REST

Maszyna wirtualna (VM) na platformie Azure jest zdefiniowane przez różne parametry, takie jak lokalizacja, rozmiar sprzętu, obraz systemu operacyjnego i poświadczenia logowania. W tym artykule przedstawiono sposób tworzenia maszyny wirtualnej systemu Linux, który korzysta z uwierzytelniania SSH za pomocą interfejsu API REST.

Aby utworzyć lub zaktualizować maszyny wirtualnej, należy użyć następującego *PUT* operacji:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

## <a name="create-a-request"></a>Utwórz żądanie

Aby utworzyć *PUT* żądania, `{subscription-id}` parametr jest wymagany. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). Należy zdefiniować `{resourceGroupName}` i `{vmName}` zasobów, wraz z `api-version` parametru. W tym artykule wykorzystano `api-version=2017-12-01`.

Wymagane są następujące nagłówki:

| Nagłówek żądania   | Opis |
|------------------|-----------------|
| *Typ zawartości:*  | Wymagany. Ustaw `application/json`. |
| *Autoryzacji:* | Wymagany. Ustaw prawidłową `Bearer` [token dostępu](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Aby uzyskać więcej informacji o sposobie tworzenia żądania, zobacz [składniki interfejsu API REST żądania/odpowiedzi](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Utwórz treść żądania

Następujące wspólne definicje są używane do tworzenia treści żądania:

| Name (Nazwa)                       | Wymagane | Typ                                                                                | Opis  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | ciąg                                                                              | Lokalizacja zasobu. |
| name                       |          | ciąg                                                                              | Nazwa maszyny wirtualnej. |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Określa ustawienia sprzętu dla maszyny wirtualnej. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Określa ustawienia przechowywania dla dysków maszyny wirtualnej. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Określa ustawienia systemu operacyjnego dla maszyny wirtualnej. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Określa interfejsy sieciowe maszyny wirtualnej. |

Aby uzyskać pełną listę dostępnych definicji w treści żądania, zobacz [maszyn wirtualnych Tworzenie lub aktualizowanie definicji treści żądania](/rest/api/compute/virtualmachines/createorupdate#definitions).

### <a name="example-request-body"></a>Przykład treści żądania

Następujący przykład treści żądania definiuje obraz 18.04 LTS Ubuntu, który używa dysków zarządzanych w warstwie Premium. Uwierzytelnianie klucza publicznego SSH jest używana, a maszyna wirtualna używa istniejącego wirtualnego karty interfejsu sieciowego (NIC) czy masz [wcześniej utworzony](../../virtual-network/virtual-network-network-interface.md). Podaj klucz publiczny SSH w *osProfile.linuxConfiguration.ssh.publicKeys.keyData* pola. W razie potrzeby można [wygenerowanie pary kluczy SSH](mac-create-ssh-keys.md).

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

Istnieją dwa pomyślnej odpowiedzi dla operacji można utworzyć ani zaktualizować maszyny wirtualnej:

| Name (Nazwa)        | Typ                                                                              | Opis |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [Maszyny wirtualnej](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 utworzono | [Maszyny wirtualnej](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Utworzone     |

Aby uzyskać więcej informacji na temat interfejsu API REST odpowiedzi, zobacz [przetworzyć komunikatu odpowiedzi](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Przykładowa odpowiedź

Skrócone *201 utworzono* przedstawiono odpowiedzi z poprzedniego treści żądania przykład, która tworzy Maszynę wirtualną *vmId* przypisano i *provisioningState* jest *Tworzenie*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na interfejsów API REST Azure lub innych narzędzi zarządzania, takich jak Azure CLI w wersji 2.0 lub Azure PowerShell zobacz następujące tematy:

- [Dostawca usługi Azure obliczeniowe interfejsu API REST](/rest/api/compute/)
- [Wprowadzenie do interfejsu API REST Azure](/rest/api/azure/)
- [Interfejs wiersza polecenia platformy Azure 2.0](/cli/azure/)
- [Modułu Azure PowerShell](/powershell/azure/overview)
