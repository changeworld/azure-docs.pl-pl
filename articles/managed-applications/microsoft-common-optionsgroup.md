---
title: Element interfejsu użytkownika usługi Azure Options Microsoft Docs
description: Opisuje element interfejsu użytkownika Microsoft. Common. options dla Azure Portal. Umożliwia użytkownikom wybór opcji dostępnych podczas wdrażania aplikacji zarządzanej.
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
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 7bec506575f1c526e59487edb67349cdf862f1a7
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331673"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft. Common. options — element interfejsu użytkownika
Kontrolka wyboru z wierszem dostępnych opcji.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika
![Microsoft. Common. Options —](./media/managed-application-elements/microsoft.common.optionsgroup.png)

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
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="remarks"></a>Uwagi
- Etykieta dla `constraints.allowedValues` jest wyświetlanym tekstem elementu, a jego wartość jest wartością wyjściową elementu, gdy jest zaznaczone.
- Jeśli jest określony, wartość domyślna musi być etykietą obecną w `constraints.allowedValues`. Jeśli nie zostanie określony, domyślnie zaznaczony jest pierwszy element w `constraints.allowedValues`. Wartość domyślna to **null**.
- `constraints.allowedValues` musi mieć co najmniej jeden element.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
```json
"two"
```

## <a name="next-steps"></a>Następne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
