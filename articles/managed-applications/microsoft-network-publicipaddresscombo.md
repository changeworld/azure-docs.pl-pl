---
title: Azure PublicIpAddressCombo UI element | Microsoft Docs
description: Opis elementu Microsoft.Network.PublicIpAddressCombo interfejsu użytkownika do portalu Azure.
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
ms.openlocfilehash: d06a450595a53fdc65fba74791345abe3a1b3db4
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109573"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI element
Grupa służy do wybierania nowego lub istniejącego publicznego adresu IP.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Jeśli użytkownik wybierze opcję "Brak" publicznego adresu IP, pole tekstowe etykieta nazwy domeny jest ukryty.
- Jeśli użytkownik wybierze istniejącego publicznego adresu IP, pole tekstowe etykieta nazwy domeny jest wyłączone. Wartość jest etykieta nazwy domeny wybranego adresu IP.
- Aktualizacje sufiks (na przykład westus.cloudapp.azure.com) nazwa domeny automatycznie na podstawie wybranej lokalizacji.

## <a name="schema"></a>Schemat
```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="remarks"></a>Uwagi
- Jeśli `constraints.required.domainNameLabel` ustawiono **true**, użytkownik musi podać etykieta nazwy domeny, podczas tworzenia nowego publicznego adresu IP. Istniejącego publicznego adresu IP, adresy bez etykiety nie są dostępne do wyboru.
- Jeśli `options.hideNone` ustawiono **true**, następnie wybrać opcję **Brak** publicznego adresu IP adres jest ukryty. Wartość domyślna to **false**.
- Jeśli `options.hideDomainNameLabel` ustawiono **true**, a następnie w polu tekstowym dla etykiety nazwy domeny jest ukryty. Wartość domyślna to **false**.
- Jeśli `options.hideExisting` ma wartość true, a następnie użytkownik nie jest możliwe wybranie istniejącego publicznego adresu IP. Wartość domyślna to **false**.
- Aby uzyskać `zone`, tylko publiczne adresy IP dla określonej strefy lub odporność publiczne adresy IP strefy są dostępne.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
Jeśli użytkownik wybierze żadnego publicznego adresu IP, formantu zwraca następujące dane wyjściowe:

```json
{
  "newOrExistingOrNone": "none"
}
```

Jeśli użytkownik wybierze nowy lub istniejący adres IP, formantu zwraca następujące dane wyjściowe:

```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "newOrExistingOrNone": "new"
}
```

- Gdy `options.hideNone` jest określony jako **true**, `newOrExistingOrNone` będzie mieć tylko wartość **nowe** lub **istniejących**.
- Gdy `options.hideDomainNameLabel` jest określony jako **true**, `domainNameLabel` jest niezadeklarowany.

## <a name="next-steps"></a>Kolejne kroki
* Aby obejrzeć wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie CreateUiDefinition](create-uidefinition-overview.md).
* Opis właściwości wspólnych elementów interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
