---
title: Błędy zasobów platformy Azure nadrzędnego | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób naprawić błędy podczas pracy z zasobu nadrzędnego.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.author: tomfitz
ms.openlocfilehash: 19642138105f3de0e2bda3f0f80e12cf74a35709
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61059994"
---
# <a name="resolve-errors-for-parent-resources"></a>Rozwiązywanie błędów dla nadrzędnej zasobów

W tym artykule opisano błędy, które mogą wystąpić podczas wdrażania zasobu, który jest zależny od zasobu nadrzędnego.

## <a name="symptom"></a>Objaw

Podczas wdrażania zasobu, który jest elementem podrzędnym do innego zasobu, może zostać wyświetlony następujący błąd:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Przyczyna

Jeśli jeden zasób jest element podrzędny inny zasób, zasób nadrzędny musi istnieć przed utworzeniem zasobów podrzędnych. Nazwa zasobu podrzędnego definiuje połączenie z zasobem nadrzędnym. Nazwa zasobu podrzędnego jest w formacie `<parent-resource-name>/<child-resource-name>`. Na przykład bazę danych SQL może być zdefiniowany jako:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Jeśli wdrożenie zarówno serwera, jak i bazy danych, w tym samym szablonie, ale nie określaj zależności na serwerze, wdrożenie bazy danych może uruchomić, zanim serwer został wdrożony. 

Jeśli zasób nadrzędny już istnieje i nie jest wdrożony w tym samym szablonie, możesz ten błąd wystąpi podczas usługi Resource Manager nie można skojarzyć zasobu podrzędnego z elementu nadrzędnego. Ten błąd może się zdarzyć, gdy zasób podrzędny nie jest w poprawnym formacie lub zasobów podrzędnych jest wdrażany do grupy zasobów, który jest inny niż grupa zasobów dla zasobu nadrzędnego.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, gdy nadrzędne i podrzędne zasoby są wdrożone w tym samym szablonie, należy dołączyć zależności.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Aby rozwiązać ten problem, gdy zasób nadrzędny była wcześniej wdrożona w innym szablonie, nie jest ustawiana jest zależność. Zamiast tego należy wdrożyć element podrzędny w tej samej grupie zasobów i podaj nazwę zasobu nadrzędnego.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlServerName": {
            "type": "string"
        },
        "databaseName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2014-04-01",
            "type": "Microsoft.Sql/servers/databases",
            "location": "[resourceGroup().location]",
            "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
            "properties": {
                "collation": "SQL_Latin1_General_CP1_CI_AS",
                "edition": "Basic"
            }
        }
    ],
    "outputs": {}
}
```

Aby uzyskać więcej informacji, zobacz [definiowania porządku wdrażania zasobów w szablonach usługi Azure Resource Manager](resource-group-define-dependencies.md).