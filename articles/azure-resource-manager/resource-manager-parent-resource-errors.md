---
title: Błędy zasobów Azure nadrzędnego | Dokumentacja firmy Microsoft
description: Opisuje sposób Rozwiązywanie błędów podczas pracy z zasobu nadrzędnego.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: c996a644f206051cb58522065f87f95a4058cdee
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357779"
---
# <a name="resolve-errors-for-parent-resources"></a>Rozwiązywanie błędów dla nadrzędnej zasobów

W tym artykule opisano błędy, które można napotkać podczas wdrażania zasobu, który jest zależny od zasobu nadrzędnego.

## <a name="symptom"></a>Objaw

W przypadku wdrażania z zasobem, który jest elementem podrzędnym do innego zasobu, może zostać wyświetlony następujący błąd:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Przyczyna

Gdy jeden zasób jest elementem podrzędnym do innego zasobu, zasobu nadrzędnego musi istnieć przed utworzeniem zasobu podrzędnego. Nazwa zasobu podrzędnego zawiera nazwę nadrzędnej. Na przykład można zdefiniować jako bazy danych SQL:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Jednak jeśli nie określisz zależności na serwerze, wdrożenie bazy danych może uruchomić przed serwera została wdrożona.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy uwzględnić zależności.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Aby uzyskać więcej informacji, zobacz [zdefiniować kolejność wdrażania zasobów w szablonach usługi Azure Resource Manager](resource-group-define-dependencies.md).