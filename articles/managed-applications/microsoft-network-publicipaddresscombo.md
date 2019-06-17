---
title: Azure PublicIpAddressCombo UI element | Microsoft Docs
description: Opis elementu Microsoft.Network.PublicIpAddressCombo interfejsu użytkownika dla witryny Azure portal.
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
ms.openlocfilehash: c3e8c99f6648f0f4927140f3215978566afb9eb8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60251087"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI element
Grupa służy do wybierania nowego lub istniejącego publicznego adresu IP.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Jeśli użytkownik wybierze opcję "None", aby uzyskać publiczny adres IP, pole tekstowe etykiety nazwy domeny jest ukryty.
- Jeśli użytkownik wybierze istniejącego publicznego adresu IP, pole tekstowe etykiety nazwy domeny jest wyłączone. Jego wartość jest etykieta nazwy domeny dla wybranego adresu IP.
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
- Jeśli `constraints.required.domainNameLabel` ustawiono **true**, użytkownik musi podać etykietę nazwy domeny, podczas tworzenia nowego publicznego adresu IP. Istniejące publiczne adresy IP bez etykiety nie są dostępne do wyboru.
- Jeśli `options.hideNone` ustawiono **true**, następnie możliwość dokonania wyboru **Brak** publiczny adres IP na adres jest ukryty. Wartość domyślna to **false**.
- Jeśli `options.hideDomainNameLabel` ustawiono **true**, a następnie w polu tekstowym dla etykiety nazwy domeny jest ukryty. Wartość domyślna to **false**.
- Jeśli `options.hideExisting` ma wartość true, użytkownik nie jest w stanie wybrać istniejącego publicznego adresu IP. Wartość domyślna to **false**.
- Aby uzyskać `zone`tylko publiczne adresy IP dla określonej strefy i odporne na błędy publiczne adresy IP strefy są dostępne.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
Jeśli użytkownik wybierze Brak publicznego adresu IP, formant zwraca następujące wyniki:

```json
{
  "newOrExistingOrNone": "none"
}
```

Jeśli użytkownik wybierze nowy lub istniejący adres IP, formant zwraca następujące wyniki:

```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "sku": "Basic",
  "newOrExistingOrNone": "new"
}
```

- Gdy `options.hideNone` jest określony jako **wartość true,** , `newOrExistingOrNone` będzie mieć tylko wartość **nowe** lub **istniejących**.
- Gdy `options.hideDomainNameLabel` jest określony jako **true**, `domainNameLabel` jest niezadeklarowany.

## <a name="next-steps"></a>Kolejne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie do zasobu CreateUiDefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólne właściwości w elementach interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
