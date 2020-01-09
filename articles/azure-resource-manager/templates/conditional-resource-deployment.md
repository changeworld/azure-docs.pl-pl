---
title: Wdrożenie warunkowe z szablonami
description: Opisuje sposób warunkowego wdrażania zasobu w szablonie Azure Resource Manager.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: 8ffff0794357ed772bcaea9dce1f93fc7601295a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485119"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Wdrożenie warunkowe w szablonach Menedżer zasobów

Czasami trzeba opcjonalnie wdrożyć zasób w szablonie. Użyj elementu `condition`, aby określić, czy zasób został wdrożony. Wartość dla tego elementu jest rozpoznawana jako true lub false. Gdy wartość jest równa true, zasób jest tworzony. Gdy wartość jest równa false, zasób nie zostanie utworzony. Wartość może zostać zastosowana tylko do całego zasobu.

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

Aby zapoznać się z kompletnym przykładowym szablonem, który używa elementu `condition`, zobacz [maszynę wirtualną z nowym lub istniejącym Virtual Network, magazynem i publicznym adresem IP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

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

Jeśli używasz funkcji [odwołania](template-functions-resource.md#reference) lub [listy](template-functions-resource.md#list) z zasobem, który jest wdrażany warunkowo, funkcja jest oceniana, nawet jeśli zasób nie został wdrożony. Występuje błąd, jeśli funkcja odwołuje się do zasobu, który nie istnieje.

Użyj funkcji [if](template-functions-logical.md#if) , aby upewnić się, że funkcja jest obliczana tylko pod kątem warunków, gdy zasób jest wdrożony. Zobacz [funkcję if](template-functions-logical.md#if) , aby zapoznać się z przykładowym szablonem, który używa if i Reference z wdrożonym warunkowo zasobem.

Zasób jest ustawiany [jako zależny od](define-resource-dependency.md) zasobu warunkowego dokładnie tak samo jak w przypadku każdego innego zasobu. Gdy zasób warunkowy nie zostanie wdrożony, Azure Resource Manager automatycznie usuwa go z wymaganych zależności.

## <a name="condition-with-complete-mode"></a>Warunek z trybem ukończenia

Jeśli szablon zostanie wdrożony z [trybem kompletnym](deployment-modes.md) , a zasób nie zostanie wdrożony, ponieważ warunek zwróci wartość false, wynik zależy od używanej wersji interfejsu API REST do wdrożenia szablonu. W przypadku używania wersji wcześniejszej niż 2019-05-10 zasób nie zostanie **usunięty**. W przypadku 2019-05-10 lub nowszych zasób **jest usuwany**. Najnowsze wersje Azure PowerShell i interfejsu wiersza polecenia platformy Azure usuwają zasób, gdy warunek ma wartość false.

## <a name="next-steps"></a>Następne kroki

* Zalecenia dotyczące tworzenia szablonów można znaleźć w temacie [Azure Resource Manager Best Practices Template](template-best-practices.md).
* Aby utworzyć wiele wystąpień zasobu, zapoznaj się z tematem " [Resource, Property" lub "Zmienna iteracji" w szablonach Azure Resource Manager](create-multiple-instances.md).