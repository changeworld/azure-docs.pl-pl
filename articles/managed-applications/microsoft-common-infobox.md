---
title: Usługa Azure elementu interfejsu użytkownika w pole informacyjne | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Common.TextBlock interfejsu użytkownika dla witryny Azure portal.
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
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 2330197b4512dfdd72de3529145103b644594e25
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252089"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Element Microsoft.Common.InfoBox interfejsu użytkownika
Formant, który dodaje pole informacyjne. Pole zawiera tekst ważne lub ostrzeżenia, które pomagają użytkownikom zrozumienie wartości, które zwracają się. Można go także połączyć z identyfikatora URI, aby uzyskać więcej informacji.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika
![Microsoft.Common.InfoBox](./media/managed-application-elements/microsoft.common.infobox.png)


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

* Dla `icon`, użyj **Brak**, **informacje**, **ostrzeżenie**, lub **błąd**.
* `uri` Właściwość jest opcjonalna.

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="next-steps"></a>Kolejne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie do zasobu CreateUiDefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólne właściwości w elementach interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
