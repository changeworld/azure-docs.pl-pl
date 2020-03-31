---
title: Element interfejsu użytkownika VirtualNetworkCombo
description: W tym artykule opisano element interfejsu użytkownika microsoft.network.virtualnetworkcombo dla witryny Azure portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 53c9653b44a6c9d26d49d37b351cf6000676e2d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651971"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkEl interfejsu użytkownika

Grupa formantów do wybierania nowej lub istniejącej sieci wirtualnej.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

Gdy użytkownik wybierze nową sieć wirtualną, użytkownik może dostosować nazwę i prefiks każdej podsieci. Konfigurowanie podsieci jest opcjonalne.

![Microsoft.Network.VirtualNetworkKomunikowanie nowego](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

Gdy użytkownik wybierze istniejącą sieć wirtualną, użytkownik musi mapować każdą podsieć, których szablon wdrożenia wymaga do istniejącej podsieci. W takim przypadku wymagane jest skonfigurowanie podsieci.

![Microsoft.Network.VirtualNetworkCombo istniejące](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-existing.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="remarks"></a>Uwagi

- Jeśli zostanie określony, pierwszy nienakładający się prefiks adresu o rozmiarze `defaultValue.addressPrefixSize` jest określany automatycznie na podstawie istniejących sieci wirtualnych w subskrypcji użytkownika.
- Wartość domyślna `defaultValue.name` `defaultValue.addressPrefixSize` dla i jest **null**.
- `constraints.minAddressPrefixSize`muszą być określone. Wszystkie istniejące sieci wirtualne o przestrzeni adresowej mniejszej niż określona wartość są niedostępne do wyboru.
- `subnets`musi być określona i `constraints.minAddressPrefixSize` musi być określona dla każdej podsieci.
- Podczas tworzenia nowej sieci wirtualnej prefiks adresu każdej podsieci jest obliczany automatycznie na `addressPrefixSize`podstawie prefiksu adresu sieci wirtualnej i odpowiedniego .
- W przypadku korzystania z istniejącej sieci wirtualnej `constraints.minAddressPrefixSize` wszystkie podsieci mniejsze niż odpowiednie są niedostępne do wyboru. Ponadto jeśli określono, podsieci, które nie `minAddressCount` mają co najmniej dostępnych adresów są niedostępne do wyboru. Wartość domyślna to **0**. Aby upewnić się, że dostępne adresy `requireContiguousAddresses`są ciągłe, należy określić wartość **true** for . Wartość domyślna to **true**.
- Tworzenie podsieci w istniejącej sieci wirtualnej nie jest obsługiwane.
- Jeśli `options.hideExisting` jest **true**, użytkownik nie może wybrać istniejącej sieci wirtualnej. Wartość domyślna to **fałsz**.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).
