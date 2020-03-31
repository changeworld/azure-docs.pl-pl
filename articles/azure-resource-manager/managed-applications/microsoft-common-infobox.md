---
title: Element interfejsu użytkownika infobox
description: W tym artykule opisano element interfejsu użytkownika witryny Microsoft.Common.InfoBox dla witryny Azure portal. Służy do dodawania tekstu lub ostrzeżeń podczas wdrażania aplikacji zarządzanej.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 6d1e4a84904ef7022d9ce85803bf10285bf0b8ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652478"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Element interfejsu użytkownika microsoft.common.infobox

Formant, który dodaje pole informacji. Pole zawiera ważny tekst lub ostrzeżenia, które pomagają użytkownikom zrozumieć wartości, które dostarczają. Można również połączyć się z identyfikatorem URI, aby uzyskać więcej informacji.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

![Skrzynka microsoft.common.infobox](./media/managed-application-elements/microsoft.common.infobox.png)


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

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>Uwagi

* Dla `icon`, użyj **Brak**, **Informacje**, **Ostrzeżenie**lub **Błąd**.
* Właściwość `uri` jest opcjonalna.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).
