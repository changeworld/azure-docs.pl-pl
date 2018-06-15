---
title: Azure elementu interfejsu użytkownika w sekcji | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Common.Section interfejsu użytkownika do portalu Azure.
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
ms.openlocfilehash: e6d7d5d7b205d275c72e96df527a354b072a9dd3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260973"
---
# <a name="microsoftcommonsection-ui-element"></a>Element Microsoft.Common.Section interfejsu użytkownika
Formant, który grupuje co najmniej jeden element w pozycji.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika
![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>Schemat
```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Some section",
  "elements": [
    {
      "name": "element1",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 1"
    },
    {
      "name": "element2",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Uwagi
- `elements` musi zawierać co najmniej jeden element i może zawierać wszystkie typy elementów, z wyjątkiem `Microsoft.Common.Section`.
- Ten element nie obsługuje `toolTip` właściwości.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
Aby uzyskać dostęp do danych wyjściowych wartości elementów w `elements`, użyj [basics()](create-uidefinition-functions.md#basics) lub [steps()](create-uidefinition-functions.md#steps) funkcje i kropkowego:

```json
basics('section1').element1
```

Elementy typu `Microsoft.Common.Section` ma same wartości nie danych wyjściowych.

## <a name="next-steps"></a>Kolejne kroki
* Aby obejrzeć wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie CreateUiDefinition](create-uidefinition-overview.md).
* Opis właściwości wspólnych elementów interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
