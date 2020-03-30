---
title: Element interfejsu użytkownika textblock
description: Zawiera opis elementu interfejsu użytkownika narzędzia Microsoft.Common.TextBlock dla witryny Azure portal. Służy do dodawania tekstu do interfejsu.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 418056cb149f4441bac49db839a0dba40c2bb42d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652218"
---
# <a name="microsoftcommontextblock-ui-element"></a>Element interfejsu użytkownika microsoft.common.textblock

Formant, który może służyć do dodawania tekstu do interfejsu portalu.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).
