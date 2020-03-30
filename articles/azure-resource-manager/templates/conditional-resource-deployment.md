---
title: Wdrożenie warunkowe z szablonami
description: W tym artykule opisano sposób warunkowego wdrożenia zasobu w szablonie usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: f170710118c0e3de6f3643b6216ed55b83b5c7df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153424"
---
# <a name="conditional-deployment-in-arm-templates"></a>Wdrożenie warunkowe w szablonach ARM

Czasami należy opcjonalnie wdrożyć zasób w szablonie usługi Azure Resource Manager (ARM). Użyj `condition` elementu, aby określić, czy zasób jest wdrażany. Wartość dla tego elementu jest rozpoznawana jako true lub false. Gdy wartość jest true, zasób jest tworzony. Gdy wartość jest false, zasób nie jest tworzony. Wartość można zastosować tylko do całego zasobu.

## <a name="new-or-existing-resource"></a>Nowy lub istniejący zasób

Można użyć wdrożenia warunkowego, aby utworzyć nowy zasób lub użyć istniejącego. W poniższym przykładzie pokazano, jak użyć warunku do wdrożenia nowego konta magazynu lub użycia istniejącego konta magazynu.

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "2017-06-01",
  "name": "[variables('storageAccountName')]",
  "location": "[parameters('location')]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

Gdy parametr **newOrExisting** jest ustawiony na **nowy,** warunek jest oceniany na true. Konto magazynu jest wdrażane. Jednak gdy **newOrExisting** jest ustawiona na **istniejące,** warunek ocenia false i konto magazynu nie jest wdrażany.

Aby uzyskać kompletny przykładowy `condition` szablon, który używa tego elementu, zobacz [maszynę wirtualną z nową lub istniejącą siecią wirtualną, magazynem i publicznym adresem IP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="allow-condition"></a>Zezwalaj na warunek

Można przekazać w wartości parametru, który wskazuje, czy warunek jest dozwolony. Poniższy przykład wdraża serwer SQL i opcjonalnie zezwala na adresy IP platformy Azure.

```json
{
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2015-05-01-preview",
  "name": "[parameters('serverName')]",
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
      "apiVersion": "2015-05-01-preview",
      "name": "AllowAllWindowsAzureIps",
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

Aby uzyskać pełny szablon, zobacz [serwer logiczny SQL platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="runtime-functions"></a>Funkcje środowiska wykonawczego

Jeśli używasz funkcji [odwołania](template-functions-resource.md#reference) lub [listy](template-functions-resource.md#list) z zasobem, który jest warunkowo wdrożony, funkcja jest oceniana, nawet jeśli zasób nie jest wdrażany. Zostanie wyświetlony błąd, jeśli funkcja odwołuje się do zasobu, który nie istnieje.

Użyj if [funkcji,](template-functions-logical.md#if) aby upewnić się, że funkcja jest oceniana tylko dla warunków, gdy zasób jest wdrażany. Zobacz [if funkcji](template-functions-logical.md#if) dla przykładowego szablonu, który używa if i odwołania z warunkowo wdrożony zasób.

[Zasób](define-resource-dependency.md) jest tak zależny od zasobu warunkowego, jak każdy inny zasób. Gdy zasób warunkowy nie jest wdrażany, usługa Azure Resource Manager automatycznie usuwa go z wymaganych zależności.

## <a name="condition-with-complete-mode"></a>Warunek z trybem kompletnym

Jeśli wdrożysz szablon z [trybem kompletnym,](deployment-modes.md) a zasób nie zostanie wdrożony, ponieważ warunek ma wartość false, wynik zależy od wersji interfejsu API REST używanej do wdrażania szablonu. Jeśli używasz wersji wcześniejszej niż 2019-05-10, zasób **nie zostanie usunięty**. W 2019-05-10 lub nowszym zasób **jest usuwany**. Najnowsze wersje programu Azure PowerShell i interfejsu wiersza polecenia platformy Azure usuwają zasób, gdy warunek jest fałszywy.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać zalecenia dotyczące tworzenia szablonów, zobacz [Najlepsze wskazówki dotyczące szablonów ARM](template-best-practices.md).
* Aby utworzyć wiele wystąpień zasobu, zobacz [Iteracja zasobów w szablonach ARM](copy-resources.md).