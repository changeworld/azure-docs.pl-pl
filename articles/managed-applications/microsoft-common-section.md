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
ms.openlocfilehash: a48c89785e0a448609026aab53364f6cf704e948
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331654"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft. Common. Section — element interfejsu użytkownika
Kontrolka, która grupuje co najmniej jeden element w ramach nagłówka.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika
![Microsoft. Common. sekcja](./media/managed-application-elements/microsoft.common.section.png)

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
Aby uzyskać dostęp do wartości wyjściowych elementów w `elements`, użyj [podstawowych ()](create-uidefinition-functions.md#basics) lub [kroków ()](create-uidefinition-functions.md#steps) i notacji kropkowej:

```json
steps('configuration').section1.text1
```

Elementy typu `Microsoft.Common.Section` nie mają samych wartości wyjściowych.

## <a name="next-steps"></a>Następne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
