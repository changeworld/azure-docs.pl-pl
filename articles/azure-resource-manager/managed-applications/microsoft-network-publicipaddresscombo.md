---
title: Element interfejsu użytkownika PublicIpAddressCombo
description: W tym artykule opisano element interfejsu użytkownika microsoft.network.publicIpAddressCombo dla witryny Azure portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 0393673663df8f3ca580ff34e16bee910b955f8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651919"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Element interfejsu użytkownika microsoft.network.publicIpAddressCombo

Grupa formantów dotyczących wybierania nowego lub istniejącego publicznego adresu IP.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Jeśli użytkownik wybierze opcję "Brak" dla publicznego adresu IP, pole tekstowe etykiety nazwy domeny jest ukryte.
- Jeśli użytkownik wybierze istniejący publiczny adres IP, pole tekstowe etykiety nazwy domeny jest wyłączone. Jego wartość to etykieta nazwy domeny wybranego adresu IP.
- Sufiks nazwy domeny (na przykład westus.cloudapp.azure.com) jest aktualizowany automatycznie na podstawie wybranej lokalizacji.

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

Jeśli użytkownik nie wybierze publicznego adresu IP, formant zwraca następujące dane wyjściowe:

```json
{
  "newOrExistingOrNone": "none"
}
```

Jeśli użytkownik wybierze nowy lub istniejący adres IP, formant zwraca następujące dane wyjściowe:

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

- Gdy `options.hideNone` jest **true**określony `newOrExistingOrNone` jako true , będzie miał tylko wartość **nowych** lub **istniejących**.
- Gdy `options.hideDomainNameLabel` jest **true**określony `domainNameLabel` jako true , jest niezadeklarowany.

## <a name="remarks"></a>Uwagi

- Jeśli `constraints.required.domainNameLabel` jest ustawiona na **true,** użytkownik musi podać etykietę nazwy domeny podczas tworzenia nowego publicznego adresu IP. Istniejące publiczne adresy IP bez etykiety nie są dostępne do wyboru.
- Jeśli `options.hideNone` ustawiono **wartość true**, opcja wyboru **Brak** dla publicznego adresu IP jest ukryta. Wartość domyślna to **fałsz**.
- Jeśli `options.hideDomainNameLabel` ustawiona jest wartość **true,** pole tekstowe etykiety nazwy domeny jest ukryte. Wartość domyślna to **fałsz**.
- Jeśli `options.hideExisting` jest true, użytkownik nie może wybrać istniejącego publicznego adresu IP. Wartość domyślna to **fałsz**.
- W `zone`przypadku publicznych adresów IP dla określonych publicznych adresów IP strefy lub strefy są dostępne tylko publiczne adresy IP.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).
