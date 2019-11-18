---
title: Tagi zasobów dla organizacji logicznej
description: Pokazuje, jak zastosować Tagi do organizowania zasobów platformy Azure na potrzeby rozliczeń i zarządzania nimi.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: b332ae86e714d4b642f921d217d80e802fa60572
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149593"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Organizowanie zasobów platformy Azure przy użyciu tagów

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

Aby zastosować Tagi do zasobów, użytkownik musi mieć dostęp do zapisu dla tego typu zasobu. Aby zastosować Tagi do wszystkich typów zasobów, użyj roli [współautor](../role-based-access-control/built-in-roles.md#contributor) . Aby zastosować Tagi tylko do jednego typu zasobu, należy użyć roli współautor dla tego zasobu. Aby na przykład zastosować Tagi do maszyn wirtualnych, użyj [współautora maszyny wirtualnej](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="policies"></a>Zasady

Aby wymusić reguły tagowania i konwencje, można użyć [Azure Policy](../governance/policy/overview.md) . Utworzenie zasad pozwala uniknąć scenariusza wdrażania zasobów w ramach subskrypcji, które nie są zgodne z oczekiwanymi tagami w organizacji. Zamiast ręcznie stosować Tagi lub wyszukiwać zasoby, które nie są zgodne, można utworzyć zasady, które automatycznie stosują potrzebne Tagi podczas wdrażania. Tagi mogą być również stosowane do istniejących zasobów przy użyciu nowego efektu [Modyfikuj](../governance/policy/concepts/effects.md#modify) i [zadania korygowania](../governance/policy/how-to/remediate-resources.md). W poniższej sekcji przedstawiono przykładowe zasady dla tagów.

[!INCLUDE [Tag policies](../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby wyświetlić istniejące tagi dla *grupy zasobów*, użyj:

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

Ten skrypt zwraca następujący format:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Aby wyświetlić istniejące tagi dla *zasobu o określonym identyfikatorze zasobu*, użyj:

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Aby wyświetlić istniejące tagi dla *zasobu o określonej nazwie i grupie zasobów*, użyj:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Aby uzyskać *grupy zasobów, które mają konkretny tag*, użyj:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

Aby uzyskać *zasoby, które mają konkretny tag*, użyj:

```azurepowershell-interactive
(Get-AzResource -Tag @{ Dept="Finance"}).Name
```

Aby uzyskać *zasoby z określoną nazwą tagu*, użyj:

```azurepowershell-interactive
(Get-AzResource -TagName Dept).Name
```

Za każdym razem, gdy stosujesz tagi do zasobu lub grupy zasobów, istniejące tagi tego zasobu lub tej grupy zasobów są zastępowane. Dlatego konieczne jest różne podejście w zależności od tego, czy dany zasób lub dana grupa zasobów ma istniejące tagi.

Aby dodać tagi do *grupy zasobów bez istniejących tagów*, użyj:

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Aby dodać tagi do *grupy zasobów z istniejącymi tagami*, pobierz istniejące tagi, dodaj nowy tag i ponownie zastosuj tagi:

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

Aby dodać tagi do *zasobu bez istniejących tagów*, użyj:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Aby dodać tagi do *zasobu z istniejącymi tagami*, użyj:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved")
Set-AzResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Aby zastosować wszystkie Tagi z grupy zasobów do jej zasobów i *nie utrzymywać istniejących tagów w zasobach*, użyj następującego skryptu:

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Aby zastosować wszystkie Tagi z grupy zasobów do jej zasobów i *zachować istniejące Tagi dla zasobów, które nie są duplikatami*, użyj następującego skryptu:

```azurepowershell-interactive
$group = Get-AzResourceGroup "examplegroup"
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Aby usunąć wszystkie tagi, przekaż pustą tablicę skrótów:

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wyświetlić istniejące tagi dla *grupy zasobów*, użyj:

```azurecli
az group show -n examplegroup --query tags
```

Ten skrypt zwraca następujący format:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Aby wyświetlić istniejące Tagi dla *zasobu, który ma określoną nazwę, typ i grupę zasobów*, należy użyć:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

W przypadku zapętlenia przez kolekcję zasobów warto wyświetlić zasób według identyfikatora zasobu. Pełny przykład przedstawiono w dalszej części tego artykułu. Aby wyświetlić istniejące tagi dla *zasobu o określonym identyfikatorze zasobu*, użyj:

```azurecli
az resource show --id <resource-id> --query tags
```

Aby uzyskać grupy zasobów z określonym tagiem, użyj `az group list`:

```azurecli
az group list --tag Dept=IT
```

Aby uzyskać wszystkie zasoby, które mają określony tag i wartość, użyj `az resource list`:

```azurecli
az resource list --tag Dept=Finance
```

Za każdym razem, gdy stosujesz tagi do zasobu lub grupy zasobów, istniejące tagi tego zasobu lub tej grupy zasobów są zastępowane. Dlatego konieczne jest różne podejście w zależności od tego, czy dany zasób lub dana grupa zasobów ma istniejące tagi.

Aby dodać tagi do *grupy zasobów bez istniejących tagów*, użyj:

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

Aby dodać tagi do *zasobu bez istniejących tagów*, użyj:

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Aby dodać tagi do zasobu, który ma już Tagi, Pobierz istniejące Tagi, przesformatuj tę wartość i ponownie Zastosuj istniejące i nowe Tagi:

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Aby zastosować wszystkie Tagi z grupy zasobów do jej zasobów i *nie utrzymywać istniejących tagów w zasobach*, użyj następującego skryptu:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

Aby zastosować wszystkie Tagi z grupy zasobów do jej zasobów i *zachować istniejące Tagi w zasobach*, użyj następującego skryptu:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Szablony

Aby oznaczyć zasób podczas wdrażania, Dodaj element `tags` do zasobu, który jest wdrażany. Podaj nazwę i wartość tagu.

### <a name="apply-a-literal-value-to-the-tag-name"></a>Stosowanie wartości literału do nazwy tagu

W poniższym przykładzie przedstawiono konto magazynu z dwoma tagami (`Dept` i `Environment`), dla których ustawiono wartości literału:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

Aby ustawić tag na wartość DateTime, użyj [funkcji UtcNow](resource-group-template-functions-string.md#utcnow).

### <a name="apply-an-object-to-the-tag-element"></a>Stosowanie obiektu do elementu tagu

Możesz zdefiniować parametr obiektu przechowującego kilka tagów i zastosować ten obiekt do elementu tagu. Każda właściwość obiektu będzie osobnym tagiem dla zasobu. Poniższy przykład zawiera parametr o nazwie `tagValues`, który został zastosowany do elementu tagu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string-to-the-tag-name"></a>Stosowanie ciągu JSON do nazwy tagu

Aby przechowywać wiele wartości w jednym tagu, zastosuj ciąg JSON reprezentujący te wartości. Cały ciąg JSON jest przechowywany jako jeden tag, który nie może zawierać więcej niż 256 znaków. Poniższy przykład zawiera pojedynczy tag o nazwie `CostCenter`, który zawiera kilka wartości z ciągu JSON:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>Zastosuj Tagi z grupy zasobów

Aby zastosować Tagi z grupy zasobów do zasobu, użyj funkcji [resources](resource-group-template-functions-resource.md#resourcegroup) . Podczas pobierania wartości tagu użyj składni `tags.[tag-name]` zamiast składni `tags.tag-name`, ponieważ niektóre znaki nie są poprawnie analizowane w notacji kropkowej.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>Interfejs API REST

Azure Portal i program PowerShell używają [interfejsu API REST Menedżer zasobów](https://docs.microsoft.com/rest/api/resources/) w tle. Jeśli trzeba zintegrować znakowanie w innym środowisku, można uzyskać Tagi przy użyciu funkcji **Get** na identyfikatorze zasobu i zaktualizować zestaw tagów przy użyciu wywołania **patch** .

## <a name="tags-and-billing"></a>Znaczniki i rozliczanie

Za pomocą tagów można grupować dane dotyczące rozliczeń. Jeśli na przykład używasz wielu maszyn wirtualnych dla różnych organizacji, użyj tagów, aby grupować użycie według centrum kosztów. Za pomocą tagów można również klasyfikować koszty według środowiska uruchomieniowego, takich jak użycie rozliczeń dla maszyn wirtualnych działających w środowisku produkcyjnym.

Informacje o tagach można uzyskać za pomocą [interfejsów API użycia zasobów platformy Azure i RateCard](../billing/billing-usage-rate-card-overview.md) lub pliku z wartościami rozdzielanymi przecinkami (CSV). Plik użycia można pobrać z [centrum konta platformy Azure](https://account.azure.com/Subscriptions) lub Azure Portal. Aby uzyskać więcej informacji, zobacz [pobieranie lub wyświetlanie faktury rozliczeń na platformie Azure oraz danych dziennego użycia](../billing/billing-download-azure-invoice-daily-usage-date.md). Podczas pobierania pliku użycia z Centrum konta platformy Azure wybierz pozycję **wersja 2**. W przypadku usług, które obsługują Tagi z rozliczeniami, Tagi są wyświetlane w kolumnie **Tagi** .

Aby uzyskać informacje na temat operacji interfejsu API REST, zobacz [Dokumentacja interfejsu API REST rozliczeń platformy Azure](/rest/api/billing/).

## <a name="next-steps"></a>Następne kroki

* Nie wszystkie typy zasobów obsługują Tagi. Aby określić, czy można zastosować tag do typu zasobu, zobacz [obsługa tagów dla zasobów platformy Azure](tag-support.md).
* Aby zapoznać się z wprowadzeniem do korzystania z portalu, zobacz [używanie Azure Portal do zarządzania zasobami platformy Azure](manage-resource-groups-portal.md).  
