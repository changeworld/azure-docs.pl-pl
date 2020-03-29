---
title: Nadrzędne błędy zasobów
description: W tym artykule opisano sposób rozwiązywania błędów podczas pracy z zasobem nadrzędnym w szablonie usługi Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: f1847389d60ddf3c6abc70bc3309940c2246084e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76154044"
---
# <a name="resolve-errors-for-parent-resources"></a>Rozwiązywanie problemów dotyczących zasobów nadrzędnych

W tym artykule opisano błędy, które mogą wystąpić podczas wdrażania zasobu, który jest zależny od zasobu nadrzędnego.

## <a name="symptom"></a>Objaw

Podczas wdrażania zasobu, który jest podrzędny do innego zasobu, może pojawić się następujący błąd:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Przyczyna

Gdy jeden zasób jest elementem podrzędnym do innego zasobu, zasób nadrzędny musi istnieć przed utworzeniem zasobu podrzędnego. Nazwa zasobu podrzędnego definiuje połączenie z zasobem nadrzędnym. Nazwa zasobu podrzędnego jest `<parent-resource-name>/<child-resource-name>`w formacie . Na przykład baza danych SQL może być zdefiniowana jako:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Jeśli wdrożysz serwer i bazę danych w tym samym szablonie, ale nie określisz zależności od serwera, wdrożenie bazy danych może zostać rozpocznie się przed wdrożeniem serwera.

Jeśli zasób nadrzędny już istnieje i nie jest wdrażany w tym samym szablonie, ten błąd jest taki, gdy Menedżer zasobów nie może skojarzyć zasobu podrzędnego z elementem nadrzędnym. Ten błąd może wystąpić, gdy zasób podrzędny nie jest w poprawnym formacie lub zasób podrzędny jest wdrażany w grupie zasobów, która jest inna niż grupa zasobów dla zasobu nadrzędnego.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten błąd, gdy zasoby nadrzędne i podrzędne są wdrażane w tym samym szablonie, należy uwzględnić zależność.

```json
"dependsOn": [
  "[variables('databaseServerName')]"
]
```

Aby rozwiązać ten błąd, gdy zasób nadrzędny został wcześniej wdrożony w innym szablonie, nie należy ustawiać zależności. Zamiast tego wdrożyć element podrzędny do tej samej grupy zasobów i podać nazwę zasobu nadrzędnego.

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
      "type": "Microsoft.Sql/servers/databases",
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
      "location": "[resourceGroup().location]",
      "properties": {
        "collation": "SQL_Latin1_General_CP1_CI_AS",
        "edition": "Basic"
      }
    }
  ],
  "outputs": {}
}
```

Aby uzyskać więcej informacji, zobacz [Definiowanie kolejności wdrażania zasobów w szablonach usługi Azure Resource Manager](define-resource-dependency.md).