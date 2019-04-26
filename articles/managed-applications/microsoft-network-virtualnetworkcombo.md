---
title: Azure VirtualNetworkCombo UI element | Microsoft Docs
description: Opis elementu Microsoft.Network.VirtualNetworkCombo interfejsu użytkownika dla witryny Azure portal.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: b0437338b403ff19761173d08be3938d07f13f55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252499"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo UI element
Grupa służy do wybierania nowej lub istniejącej sieci wirtualnej.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika
Gdy użytkownik wybiera nową sieć wirtualną, użytkownik może dostosować nazwę każdej podsieci i prefiksu adresu. Konfigurowanie podsieci jest opcjonalne.

![Microsoft.Network.VirtualNetworkCombo new](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

Gdy użytkownik wybiera istniejącą sieć wirtualną, użytkownik musi być mapowane każdej podsieci, wymagane przez szablon wdrożenia do istniejącej podsieci. W tym przypadku Konfigurowanie podsieci jest wymagana.

![Microsoft.Network.VirtualNetworkCombo existing](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-existing.png)

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

## <a name="remarks"></a>Uwagi
- Jeśli zostanie określony, pierwszy nienakładający prefiks rozmiaru adresu `defaultValue.addressPrefixSize` jest ustalana automatycznie w istniejących sieciach wirtualnych w subskrypcji użytkownika.
- Wartością domyślną dla `defaultValue.name` i `defaultValue.addressPrefixSize` jest **null**.
- `constraints.minAddressPrefixSize` musi być określona. Niedostępny do wyboru są istniejące sieci wirtualne, z mniejszą niż określona wartość przestrzeń adresową.
- `subnets` należy określić, i `constraints.minAddressPrefixSize` musi być określona dla każdej podsieci.
- Podczas tworzenia nowej sieci wirtualnej, prefiks adresu w każdej podsieci jest obliczana automatycznie na podstawie prefiksu adresu sieci wirtualnej oraz odpowiednie `addressPrefixSize`.
- Korzystając z istniejącego wirtualnego sieci, wszelkie podsieci, które są mniejsze niż odpowiednie `constraints.minAddressPrefixSize` nie są dostępne do wyboru. Ponadto jeśli zostanie określony, podsieci, które nie mają co najmniej `minAddressCount` dostępnych adresów nie są dostępne do wyboru. Wartość domyślna to **0**. Aby upewnić się, że dostępne adresy są ciągłe, należy określić **true** dla `requireContiguousAddresses`. Wartość domyślna to **true**.
- Tworzenie podsieci w istniejącej sieci wirtualnej nie jest obsługiwane.
- Jeśli `options.hideExisting` jest **true**, użytkownik nie może wybrać istniejącą sieć wirtualną. Wartość domyślna to **false**.

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

## <a name="next-steps"></a>Kolejne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie do zasobu CreateUiDefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólne właściwości w elementach interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
