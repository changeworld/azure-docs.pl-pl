---
title: Azure elementu interfejsu użytkownika InfoBox | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Common.TextBlock interfejsu użytkownika do portalu Azure.
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
ms.date: 04/30/2018
ms.author: tomfitz
ms.openlocfilehash: 9507280bf5df1e63c81f0b2488b9676784c69812
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260878"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Element Microsoft.Common.InfoBox interfejsu użytkownika
Formant, który dodaje pole informacje.

## <a name="schema"></a>Schemat
```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="remarks"></a>Uwagi

* Dla `icon`, użyj **Brak**, **informacji**, **ostrzeżenie**, lub **błąd**.
* `uri` Właściwość jest opcjonalna.

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="next-steps"></a>Kolejne kroki
* Aby obejrzeć wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie CreateUiDefinition](create-uidefinition-overview.md).
* Opis właściwości wspólnych elementów interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
