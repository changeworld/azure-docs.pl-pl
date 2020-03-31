---
title: Tworzenie maszyny Wirtualnej systemu Linux za pomocą interfejsu API REST
description: Dowiedz się, jak utworzyć maszynę wirtualną systemu Linux na platformie Azure, która używa dysków zarządzanych i uwierzytelniania SSH za pomocą interfejsu API rest platformy Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 1594c030839cccdd48c4b032c6ad92f746f78e26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970270"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Tworzenie maszyny wirtualnej systemu Linux, która używa uwierzytelniania SSH za pomocą interfejsu API REST

Maszyna wirtualna systemu Linux (VM) na platformie Azure składa się z różnych zasobów, takich jak dyski i interfejsy sieciowe i definiuje parametry, takie jak lokalizacja, rozmiar i obraz systemu operacyjnego oraz ustawienia uwierzytelniania.

Maszynę wirtualną z systemem Linux można utworzyć za pośrednictwem witryny Azure portal, interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure 2.0, wielu zestawów SDK platformy Azure, szablonów usługi Azure Resource Manager i wielu narzędzi innych firm, takich jak Ansible lub Terraform. Wszystkie te narzędzia ostatecznie używać interfejsu API REST do tworzenia maszyny Wirtualnej systemu Linux.

W tym artykule pokazano, jak używać interfejsu API REST do tworzenia maszyny Wirtualnej z systemem Linux z systemem Ubuntu 18.04-LTS z dyskami zarządzanymi i uwierzytelnianiem SSH.

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed utworzeniem i przesłaniem żądania należy:

* Dla `{subscription-id}` twojej subskrypcji
  * Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)
* A `{resourceGroupName}` utworzony z wyprzedzeniem
* [Interfejs sieci wirtualnej](../../virtual-network/virtual-network-network-interface.md) w tej samej grupie zasobów
* Para kluczy SSH (możesz [wygenerować nową,](mac-create-ssh-keys.md) jeśli jej nie masz)

## <a name="request-basics"></a>Prośba o podstawy

Aby utworzyć lub zaktualizować maszynę wirtualną, należy użyć następującej operacji *PUT:*

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

`{subscription-id}` Oprócz parametrów `{resourceGroupName}` i, musisz określić `{vmName}` (`api-version` jest opcjonalny, ale ten `api-version=2017-12-01`artykuł został przetestowany z )

Wymagane są następujące nagłówki:

| Nagłówek żądania   | Opis |
|------------------|-----------------|
| *Content-Type:*  | Wymagany. Ustaw wartość `application/json`. |
| *Authorization:* | Wymagany. Ustaw na prawidłowy  [token dostępu](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)`Bearer`. |

Aby uzyskać ogólne informacje dotyczące pracy z żądaniami interfejsu API REST, zobacz [Składniki żądania/odpowiedzi interfejsu API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Tworzenie treści żądania

Następujące typowe definicje są używane do tworzenia treści żądania:

| Nazwa                       | Wymagany | Typ                                                                                | Opis  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | ciąg                                                                              | Lokalizacja zasobu. |
| name                       |          | ciąg                                                                              | Nazwa maszyny wirtualnej. |
| właściwości.hardwareProfile |          | [Profil sprzętu](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Określa ustawienia sprzętowe maszyny wirtualnej. |
| właściwości.storageProfile  |          | [Profil przechowywania](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Określa ustawienia magazynu dla dysków maszyny wirtualnej. |
| właściwości.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Określa ustawienia systemu operacyjnego dla maszyny wirtualnej. |
| właściwości.networkProfile  |          | [Profil sieciowy](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Określa interfejsy sieciowe maszyny wirtualnej. |

Przykładowa treść żądania znajduje się poniżej. Upewnij się, że nazwa maszyny `{computerName}` `{name}` Wirtualnej jest określona w parametrach `networkInterfaces`i parametrach, nazwa interfejsu sieciowego utworzonego w obszarze , nazwa `~/.ssh/id_rsa.pub`użytkownika `keyData`i `adminUsername` `path`, oraz *publiczna* część klawiatury SSH (znajdująca się na przykład w . Inne parametry, które można `location` `vmSize`zmodyfikować, obejmują i .  

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

Aby uzyskać pełną listę dostępnych definicji w treści żądania, zobacz [Maszyny wirtualne tworzenia lub aktualizowania definicji treści żądania](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>Wysyłanie żądania

Możesz użyć klienta preferencji do wysyłania tego żądania HTTP. Możesz również użyć [narzędzia w przeglądarce,](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) klikając przycisk **Wypróbuj.**

### <a name="responses"></a>Odpowiedzi

Istnieją dwie pomyślne odpowiedzi dla operacji, aby utworzyć lub zaktualizować maszynę wirtualną:

| Nazwa        | Typ                                                                              | Opis |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 ok.      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Utworzono | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Utworzone     |

Skondensowana *201 Utworzona* odpowiedź z poprzedniej treści żądania przykładu, która tworzy maszynę *wirtualną pokazuje,* że maszyna wirtualna została przypisana, a *agregat inicjowania obsługi administracyjnej* *tworzy:*

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Aby uzyskać więcej informacji na temat odpowiedzi interfejsu API REST, zobacz [Przetwarzanie komunikatu odpowiedzi](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsów API rest platformy Azure lub innych narzędzi do zarządzania, takich jak interfejs wiersza polecenia platformy Azure lub program Azure PowerShell, zobacz następujące:

- [Interfejs API REST dostawcy obliczeń platformy Azure](/rest/api/compute/)
- [Get started with Azure REST API (Rozpoczęcie pracy z interfejsem API REST platformy Azure)](/rest/api/azure/)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/)
- [Moduł programu Azure PowerShell](/powershell/azure/overview)
