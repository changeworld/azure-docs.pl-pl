---
title: Azure PublicIpAddressCombo UI element | Microsoft Docs
description: Opisuje element interfejsu użytkownika Microsoft. Network. PublicIpAddressCombo dla Azure Portal.
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
ms.openlocfilehash: 069a8ee1f019d1b21be996084e4902f94076fbf7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151489"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI element

Grupa kontrolek służących do wybierania nowego lub istniejącego publicznego adresu IP.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Jeśli użytkownik wybierze opcję "Brak" dla publicznego adresu IP, pole tekstowe etykieta nazwy domeny jest ukryte.
- Jeśli użytkownik wybierze istniejący publiczny adres IP, pole tekstowe etykieta nazwy domeny jest wyłączone. Jego wartość to etykieta nazwy domeny wybranego adresu IP.
- Sufiks nazwy domeny (na przykład westus.cloudapp.azure.com) są automatycznie aktualizowane na podstawie wybranej lokalizacji.

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

## <a name="sample-output"></a>Przykładowe dane wyjściowe

Jeśli użytkownik nie wybierze publicznego adresu IP, kontrolka zwróci następujące dane wyjściowe:

```json
{
  "newOrExistingOrNone": "none"
}
```

Jeśli użytkownik wybierze nowy lub istniejący adres IP, kontrolka zwróci następujące dane wyjściowe:

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

- Gdy `options.hideNone` jest określony jako **true**, `newOrExistingOrNone` będzie miał tylko wartość **nowe** lub **istniejące**.
- Gdy `options.hideDomainNameLabel` jest określony jako **true**, `domainNameLabel` jest niezadeklarowany.

## <a name="remarks"></a>Uwagi

- Jeśli `constraints.required.domainNameLabel` ma **wartość PRAWDA**, użytkownik musi podać etykietę nazwy domeny podczas tworzenia nowego publicznego adresu IP. Istniejące publiczne adresy IP bez etykiet nie są dostępne do wyboru.
- Jeśli `options.hideNone` ma **wartość true**, wówczas opcja wyboru **Brak** dla publicznego adresu IP jest ukryta. Wartość domyślna to **false**.
- Jeśli `options.hideDomainNameLabel` ma **wartość true**, pole tekstowe etykiety nazwy domeny jest ukryte. Wartość domyślna to **false**.
- Jeśli `options.hideExisting` ma wartość true, użytkownik nie może wybrać istniejącego publicznego adresu IP. Wartość domyślna to **false**.
- W przypadku `zone`dostępne są tylko publiczne adresy IP dla określonej strefy lub strefy.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
