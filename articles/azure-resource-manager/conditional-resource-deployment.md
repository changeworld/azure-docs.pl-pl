---
title: Wdrożenie warunkowe z szablonami Azure Resource Manager
description: Opisuje sposób warunkowego wdrażania zasobu w szablonie Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: 88f8b6a8dcce0e498a7b81b8741072bcf4cfcad8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259511"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Wdrożenie warunkowe w szablonach Menedżer zasobów

Czasami trzeba opcjonalnie wdrożyć zasób w szablonie. Użyj elementu `condition` , aby określić, czy zasób został wdrożony. Wartość dla tego elementu jest rozpoznawana jako true lub false. Gdy wartość jest równa true, zasób jest tworzony. Gdy wartość jest równa false, zasób nie zostanie utworzony. Wartość może zostać zastosowana tylko do całego zasobu.

## <a name="new-or-existing-resource"></a>Nowy lub istniejący zasób

Przy użyciu wdrożenia warunkowego można utworzyć nowy zasób lub użyć istniejącego. Poniższy przykład pokazuje, jak za pomocą warunku wdrożyć nowe konto magazynu lub użyć istniejącego konta magazynu.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Gdy parametr **newOrExisting** ma wartość **New**, warunek ma wartość true. Konto magazynu zostało wdrożone. Jeśli jednak **newOrExisting** jest ustawiona na **istniejące**, warunek zwróci wartość false, a konto magazynu nie jest wdrożone.

Aby zapoznać się z kompletnym przykładowym `condition` szablonem, który używa elementu, zobacz [maszyna wirtualna z nowym lub istniejącym Virtual Network, magazynem i publicznym adresem IP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="allow-condition"></a>Zezwalaj na warunek

Można przekazać wartość parametru, która wskazuje, czy warunek jest dozwolony. Poniższy przykład wdraża program SQL Server i opcjonalnie zezwala na adresy IP platformy Azure.

```json
{
    "type": "Microsoft.Sql/servers",
    "name": "[parameters('serverName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "version": "12.0"
    },
    "resources": [
        {
            "condition": "[parameters('allowAzureIPs')]",
            "type": "firewallRules",
            "name": "AllowAllWindowsAzureIps",
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
            ],
            "properties": {
                "endIpAddress": "0.0.0.0",
                "startIpAddress": "0.0.0.0"
            }
        }
    ]
}
```

Aby zapoznać się z kompletnym szablonem, zobacz [serwer logiczny usługi Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="runtime-functions"></a>Funkcje środowiska uruchomieniowego

Jeśli używasz funkcji [odwołania](resource-group-template-functions-resource.md#reference) lub [listy](resource-group-template-functions-resource.md#list) z zasobem, który jest wdrażany warunkowo, funkcja jest oceniana, nawet jeśli zasób nie został wdrożony. Występuje błąd, jeśli funkcja odwołuje się do zasobu, który nie istnieje.

Użyj funkcji [if](resource-group-template-functions-logical.md#if) , aby upewnić się, że funkcja jest obliczana tylko pod kątem warunków, gdy zasób jest wdrożony. Zobacz [funkcję if](resource-group-template-functions-logical.md#if) , aby zapoznać się z przykładowym szablonem, który używa if i Reference z wdrożonym warunkowo zasobem.

## <a name="next-steps"></a>Następne kroki

* Zalecenia dotyczące tworzenia szablonów można znaleźć w temacie [Azure Resource Manager Best Practices Template](template-best-practices.md).
* Aby utworzyć wiele wystąpień zasobu, zapoznaj się z tematem " [Resource, Property" lub "Zmienna iteracji" w szablonach Azure Resource Manager](resource-group-create-multiple.md).