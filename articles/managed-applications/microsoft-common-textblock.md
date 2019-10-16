---
title: Element interfejsu użytkownika usługi Azure TextBlock | Microsoft Docs
description: Opisuje element Microsoft. Common. TextBlock interfejsu użytkownika dla Azure Portal. Służy do dodawania tekstu do interfejsu.
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
ms.openlocfilehash: da2453889f04352dbabe182772312d70deec4464
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331624"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft. Common. TextBlock — element interfejsu użytkownika
Kontrolka, która może służyć do dodawania tekstu do interfejsu portalu.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika
![Microsoft. Common. TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

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
* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
