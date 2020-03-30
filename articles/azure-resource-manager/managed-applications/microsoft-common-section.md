---
title: Element interfejsu użytkownika sekcji
description: Zawiera opis elementu interfejsu użytkownika witryny Microsoft.Common.Section dla witryny Azure portal. Służy do grupowania elementów w portalu do wdrażania aplikacji zarządzanych.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 999a7386ee7c5b3ef76f87280cc2d0cd45f143cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652257"
---
# <a name="microsoftcommonsection-ui-element"></a>Element interfejsu użytkownika microsoft.common.section

Formant, który grupuje jeden lub więcej elementów pod nagłówkiem.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Uwagi

- `elements`musi mieć co najmniej jeden element i `Microsoft.Common.Section`może mieć wszystkie typy elementów z wyjątkiem .
- Ten element nie obsługuje `toolTip` właściwości.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
Aby uzyskać dostęp do `elements`wartości wyjściowych elementów w programie , użyj funkcji [basics()](create-uidefinition-functions.md#basics) lub [steps()](create-uidefinition-functions.md#steps) i notacji punktowej:

```json
steps('configuration').section1.text1
```

Elementy typu `Microsoft.Common.Section` nie mają wartości wyjściowych.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).
