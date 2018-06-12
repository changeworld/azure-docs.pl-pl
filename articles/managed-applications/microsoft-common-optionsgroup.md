---
title: Azure elementu interfejsu użytkownika OptionsGroup | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Common.OptionsGroup interfejsu użytkownika do portalu Azure.
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
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 2e0b448b5ab48e7be3429d3d3b5b898b6bf22115
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261852"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Element Microsoft.Common.OptionsGroup interfejsu użytkownika
Formant wyboru z wierszem dostępnych opcji.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika
![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>Schemat
```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>Uwagi
- Etykieta dla `constraints.allowedValues` jest wyświetlany tekst dla elementu i jego wartość jest wartością danych wyjściowych w przypadku wybrania elementu.
- Jeśli jest określony, wartość domyślna musi być obecne w etykiecie `constraints.allowedValues`. Jeśli nie zostanie określony, pierwszy element `constraints.allowedValues` jest domyślnie zaznaczona. Wartość domyślna to **null**.
- `constraints.allowedValues` musi zawierać co najmniej jeden element.
- Ten element nie obsługuje `constraints.required` właściwości; można pomyślnie zweryfikować należy wybrać element.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
```json
"two"
```

## <a name="next-steps"></a>Następne kroki
* Aby obejrzeć wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie CreateUiDefinition](create-uidefinition-overview.md).
* Opis właściwości wspólnych elementów interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
