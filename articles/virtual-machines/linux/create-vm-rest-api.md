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
ms.openlocfilehash: 2b078cd769a9b4e5e66fe132fd4ef73ec4621efc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60391365"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Utwórz maszynę wirtualną systemu Linux, która korzysta z uwierzytelniania SSH przy użyciu interfejsu API REST

Linux maszyny wirtualnej (VM) na platformie Azure składa się z różnych zasobów, takich jak dyski i interfejsów sieci i definiuje parametry, takie jak ustawienia umiejscowienie, rozmiar i systemu operacyjnego obrazu i uwierzytelniania.

Możesz utworzyć Maszynę wirtualną systemu Linux w witrynie Azure portal, Azure CLI 2.0, wiele zestawów SDK usługi Azure, szablony usługi Azure Resource Manager i wiele narzędzi innych firm, takich jak Ansible lub Terraform. Te narzędzia ostatecznie Użyj interfejsu API REST do utworzenia maszyny Wirtualnej systemu Linux.

W tym artykule pokazano, jak używać interfejsu API REST do tworzenia maszyny Wirtualnej systemu Linux z systemem Ubuntu 18.04-LTS z dysków zarządzanych i uwierzytelniania SSH.

## <a name="before-you-start"></a>Przed rozpoczęciem

Aby utworzyć i przesłać żądanie, potrzebne są:

* `{subscription-id}` Dla Twojej subskrypcji
  * Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)
* Element `{resourceGroupName}` został utworzony wcześniej
* A [interfejs sieci wirtualnej](../../virtual-network/virtual-network-network-interface.md) w tej samej grupie zasobów
* Pary kluczy SSH (możesz [wygenerować nowy](mac-create-ssh-keys.md) Jeśli go masz)

## <a name="request-basics"></a>Podstawowe informacje dotyczące żądania

Aby utworzyć lub zaktualizować maszyny wirtualnej, należy użyć następującego *umieścić* operacji:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Oprócz `{subscription-id}` i `{resourceGroupName}` parametrów, należy określić `{vmName}` (`api-version` jest opcjonalny, ale w tym artykule został przetestowany przy użyciu `api-version=2017-12-01`)

Wymagane są następujące nagłówki:

| Nagłówek żądania   | Opis |
|------------------|-----------------|
| *Typ zawartości:*  | Wymagany. Ustaw `application/json`. |
| *Autoryzacja:* | Wymagany. Ustawić prawidłową `Bearer` [token dostępu](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Aby uzyskać ogólne informacje na temat pracy z żądań interfejsu API REST, zobacz [składniki żądania/odpowiedzi interfejsu API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Tworzenie treści żądania

Następujące typowe definicje są używane do tworzenia treści żądania:

| Name (Nazwa)                       | Wymagane | Typ                                                                                | Opis  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | string                                                                              | Lokalizacja zasobu. |
| name                       |          | string                                                                              | Nazwa maszyny wirtualnej. |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Określa ustawienia sprzętu dla maszyny wirtualnej. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Określa ustawienia przechowywania dla dysków maszyny wirtualnej. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Określa ustawienia systemu operacyjnego dla maszyny wirtualnej. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Określa interfejsy sieciowe maszyny wirtualnej. |

Treść żądania przykład znajduje się poniżej. Upewnij się, należy określić nazwę maszyny Wirtualnej w `{computerName}` i `{name}` parametrów, nazwa interfejsu sieciowego utworzonego w ramach `networkInterfaces`, swoją nazwę użytkownika w `adminUsername` i `path`i *publicznego*część swojej para kluczy SSH (na przykład znajduje się w `~/.ssh/id_rsa.pub`) w `keyData`. Inne parametry, możesz chcieć zmodyfikować to `location` i `vmSize`.  

```json
{
  "location": "eastus",
  "name": "{vmName}",
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
      "computerName": "{vmName}",
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
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

Aby uzyskać pełną listę dostępnych definicji w treści żądania, zobacz [maszyn wirtualnych, Utwórz lub zaktualizuj definicje treść żądania](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>Wysyła żądanie

Można użyć klienta z preferencjami do wysłania tego żądania HTTP. Można także użyć [narzędzia w przeglądarce](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) , klikając **wypróbuj** przycisku.

### <a name="responses"></a>Odpowiedzi

Istnieją dwa pomyślne odpowiedzi dla operacji, które można utworzyć lub zaktualizować maszyny wirtualnej:

| Name (Nazwa)        | Typ                                                                              | Opis |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 utworzono | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Utworzone     |

Skrócone *201 utworzono* odpowiedź z treści żądania przykład poprzedniej, która umożliwia utworzenie maszyny Wirtualnej wskazuje *vmId* przypisano i *provisioningState* jest *Tworzenie*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Aby uzyskać więcej informacji na temat odpowiedzi interfejsu API REST, zobacz [przetworzyć komunikatu odpowiedzi](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsów API REST platformy Azure lub narzędzi do zarządzania, takich jak wiersza polecenia platformy Azure lub programu Azure PowerShell zobacz następujące tematy:

- [Dostawca usługi Azure Compute interfejsu API REST](/rest/api/compute/)
- [Wprowadzenie do interfejsu API REST platformy Azure](/rest/api/azure/)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/)
- [Moduł programu Azure PowerShell](/powershell/azure/overview)
