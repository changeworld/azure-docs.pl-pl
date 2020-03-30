---
title: TagsByResource Element interfejsu użytkownika
description: W tym artykule opisano element interfejsu użytkownika microsoft.common.tagsByResource dla witryny Azure portal. Służy do stosowania tagów do zasobu podczas wdrażania.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 23a7c54a84ec083b8fa470f26582913fcc3d2ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652205"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Microsoft.Common.TagsByResource Element interfejsu użytkownika

Formant do kojarzenia tagów z [zasobami](../management/tag-resources.md) we wdrożeniu.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>Uwagi

- Należy określić co `resources` najmniej jeden element w tablicy.
- Każdy element `resources` w musi być w pełni kwalifikowany typ zasobu. Te elementy są wyświetlane w **zasobu** listy rozwijanej i są tagowalne przez użytkownika.
- Dane wyjściowe formantu są sformatowane w celu łatwego przypisywania wartości tagów w szablonie usługi Azure Resource Manager. Aby otrzymać dane wyjściowe formantu w szablonie, należy dołączyć parametr do szablonu, jak pokazano w poniższym przykładzie:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Dla każdego zasobu, który może być oznakowany, przypisz właściwość tagów do wartości parametru dla tego typu zasobu:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Użyj [if](../templates/template-functions-logical.md#if) funkcji podczas uzyskiwania dostępu do parametru tagsByResource. Umożliwia przypisanie pustego obiektu, gdy do danego typu zasobu nie są przypisane żadne znaczniki.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
- Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).
