---
title: Element interfejsu użytkownika sekcji platformy Azure | Microsoft Docs
description: Opisuje element interfejsu użytkownika Microsoft. Common. Section dla Azure Portal. Służy do grupowania elementów w portalu na potrzeby wdrażania aplikacji zarządzanych.
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
ms.openlocfilehash: fd2c1105078b918043791fd0f18395409bb32f7c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151711"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft. Common. Section — element interfejsu użytkownika

Kontrolka, która grupuje co najmniej jeden element w ramach nagłówka.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

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

- `elements` musi mieć co najmniej jeden element i może zawierać wszystkie typy elementów z wyjątkiem `Microsoft.Common.Section`.
- Ten element nie obsługuje właściwości `toolTip`.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
Aby uzyskać dostęp do wartości wyjściowych elementów w `elements`, użyj [podstawowych ()](create-uidefinition-functions.md#basics) lub [czynności ()](create-uidefinition-functions.md#steps) i notacji kropkowej:

```json
steps('configuration').section1.text1
```

Elementy typu `Microsoft.Common.Section` nie mają samych wartości wyjściowych.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
