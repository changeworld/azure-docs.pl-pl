---
title: Tagi zasobów dla organizacji logicznej
description: Pokazuje, jak zastosować Tagi do organizowania zasobów platformy Azure na potrzeby rozliczeń i zarządzania nimi.
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 5751f2d1bc123c5918ae0fabc5b908b5f4fec71d
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087313"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Organizowanie zasobów platformy Azure przy użyciu tagów

Znaczniki do zasobów platformy Azure są stosowane w celu logicznego organizowania ich w taksonomię. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

Po zastosowaniu tagów można pobrać wszystkie zasoby w subskrypcji o nazwie i wartości konkretnego tagu. Tagi umożliwiają pobieranie pokrewnych zasobów z różnych grup zasobów. To pomocne rozwiązanie, gdy trzeba zorganizować zasoby w celach rozliczeniowych lub zarządzania.

Taksonomia powinna uwzględniać samoobsługową strategię tagowania metadanych, a także strategię automatycznego tagowania, która pozwala zmniejszyć obciążenie użytkownikami i zwiększyć dokładność.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="limitations"></a>Ograniczenia

Tagi mają następujące ograniczenia:

* Nie wszystkie typy zasobów obsługują Tagi. Aby określić, czy można zastosować tag do typu zasobu, zobacz [obsługa tagów dla zasobów platformy Azure](tag-support.md).
* Każdy zasób lub Grupa zasobów może mieć maksymalnie 50 par nazwa/wartość tagu. Jeśli musisz zastosować więcej tagów niż maksymalna dozwolona liczba, użyj ciągu JSON dla wartości tagu. Ciąg JSON może zawierać wiele wartości, które są stosowane do jednej nazwy tagu. Grupa zasobów może zawierać wiele zasobów, dla których każda z nich ma 50 par nazwa/wartość.
* Nazwa tagu może zawierać maksymalnie 512 znaków, a wartość tagu jest ograniczona do 256 znaków. W przypadku kont magazynu nazwa tagu jest ograniczona do 128 znaków, a wartość tagu jest ograniczona do 256 znaków.
* Uogólnione maszyny wirtualne nie obsługują tagów.
* Tagi zastosowane do grupy zasobów nie są dziedziczone przez zasoby należące do tej grupy.
* Nie można zastosować tagów do zasobów klasycznych, takich jak Cloud Services.
* Nazwy tagów nie mogą zawierać następujących znaków: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Obecnie Azure DNS strefy i usługi Traffic Manager nie umożliwiają również używania spacji w tagu. 

## <a name="required-access"></a>Wymagany dostęp

Aby zastosować Tagi do zasobów, użytkownik musi mieć dostęp do zapisu dla tego typu zasobu. Aby zastosować Tagi do wszystkich typów zasobów, użyj roli [współautor](../../role-based-access-control/built-in-roles.md#contributor) . Aby zastosować Tagi tylko do jednego typu zasobu, należy użyć roli współautor dla tego zasobu. Aby na przykład zastosować Tagi do maszyn wirtualnych, użyj [współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="policies"></a>Zasady

Aby wymusić reguły tagowania i konwencje, można użyć [Azure Policy](../../governance/policy/overview.md) . Utworzenie zasad pozwala uniknąć scenariusza wdrażania zasobów w ramach subskrypcji, które nie są zgodne z oczekiwanymi tagami w organizacji. Zamiast ręcznie stosować Tagi lub wyszukiwać zasoby, które nie są zgodne, można utworzyć zasady, które automatycznie stosują potrzebne Tagi podczas wdrażania. Tagi mogą być również stosowane do istniejących zasobów przy użyciu nowego efektu [Modyfikuj](../../governance/policy/concepts/effects.md#modify) i [zadania korygowania](../../governance/policy/how-to/remediate-resources.md). W poniższej sekcji przedstawiono przykładowe zasady dla tagów.

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>Program PowerShell

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

Aby wyświetlić istniejące Tagi dla zasobu, *który ma określoną nazwę i grupę zasobów*, użyj:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Lub, jeśli masz identyfikator zasobu dla zasobu, możesz przekazać ten identyfikator zasobu, aby uzyskać Tagi.

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Aby uzyskać *grupy zasobów z określoną nazwą i wartością tagu*, użyj:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "Dept"="Finance" }).ResourceGroupName
```

Aby uzyskać *zasoby z określoną nazwą i wartością tagu*, użyj:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "Dept"="Finance"}).Name
```

Aby uzyskać *zasoby z określoną nazwą tagu*, użyj:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Za każdym razem, gdy stosujesz tagi do zasobu lub grupy zasobów, istniejące tagi tego zasobu lub tej grupy zasobów są zastępowane. Dlatego konieczne jest różne podejście w zależności od tego, czy dany zasób lub dana grupa zasobów ma istniejące tagi.

Aby dodać tagi do *grupy zasobów bez istniejących tagów*, użyj:

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ "Dept"="IT"; "Environment"="Test" }
```

Aby dodać tagi do *grupy zasobów z istniejącymi tagami*, pobierz istniejące tagi, dodaj nowy tag i ponownie zastosuj tagi:

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

Aby dodać tagi do *zasobu bez istniejących tagów*, użyj:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $resource.ResourceId -Force
```

Może istnieć więcej niż jeden zasób o tej samej nazwie w grupie zasobów. W takim przypadku można ustawić każdy zasób przy użyciu następujących poleceń:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Force }
```

Aby dodać tagi do *zasobu z istniejącymi tagami*, użyj:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$resource.Tags.Add("Status", "Approved")
Set-AzResource -Tag $resource.Tags -ResourceId $resource.ResourceId -Force
```

Aby zastosować wszystkie Tagi z grupy zasobów do jej zasobów i *nie utrzymywać istniejących tagów w zasobach*, użyj następującego skryptu:

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
Get-AzResource -ResourceGroupName $group.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $group.Tags -Force }
```

Aby zastosować wszystkie Tagi z grupy zasobów do jej zasobów i *zachować istniejące Tagi dla zasobów, które nie są duplikatami*, użyj następującego skryptu:

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
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

```azurecli-interactive
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

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

W przypadku zapętlenia przez kolekcję zasobów warto wyświetlić zasób według identyfikatora zasobu. Pełny przykład przedstawiono w dalszej części tego artykułu. Aby wyświetlić istniejące tagi dla *zasobu o określonym identyfikatorze zasobu*, użyj:

```azurecli-interactive
az resource show --id <resource-id> --query tags
```

Aby uzyskać grupy zasobów z określonym tagiem, użyj `az group list`:

```azurecli-interactive
az group list --tag Dept=IT
```

Aby uzyskać wszystkie zasoby, które mają określony tag i wartość, użyj `az resource list`:

```azurecli-interactive
az resource list --tag Dept=Finance
```

Dodając Tagi do grupy zasobów lub zasobu, można zastąpić istniejące Tagi lub dołączyć nowe tagi do istniejących tagów.

Aby zastąpić istniejące Tagi w grupie zasobów, użyj:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Aby dołączyć tag do istniejących tagów w grupie zasobów, użyj:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

Aby zastąpić Tagi w zasobie, użyj:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Aby dołączyć tag do istniejących tagów w zasobie, użyj:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Aby zastosować wszystkie Tagi z grupy zasobów do jej zasobów i *nie utrzymywać istniejących tagów w zasobach*, użyj następującego skryptu:

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags $tags --id $id
done
```

Aby zastosować wszystkie Tagi z grupy zasobów do jej zasobów i *zachować istniejące Tagi w zasobach*, użyj następującego skryptu:

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')

resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  resourcejsontags=$(az resource show --id $id --query tags -o json)
  resourcetags=$(echo $resourcejsontags | tr -d '"{},' | sed 's/: /=/g')
  az resource tag --tags $tags$resourcetags --id $id
done
```

Jeśli nazwy tagów lub wartości zawierają spacje, należy wykonać kilka dodatkowych kroków. Poniższy przykład stosuje wszystkie Tagi z grupy zasobów do jej zasobów, gdy Tagi mogą zawierać spacje.

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '{}"' | sed 's/: /=/g' | sed "s/\"/'/g" | sed 's/, /,/g' | sed 's/ *$//g' | sed 's/^ *//g')
origIFS=$IFS
IFS=','
read -a tagarr <<< "$tags"
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags "${tagarr[@]}" --id $id
done
IFS=$origIFS
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

Aby ustawić tag na wartość DateTime, użyj [funkcji UtcNow](../templates/template-functions-string.md#utcnow).

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

Aby zastosować Tagi z grupy zasobów do zasobu, użyj funkcji [resources](../templates/template-functions-resource.md#resourcegroup) . Podczas pobierania wartości tagu użyj składni `tags[tag-name]` zamiast składni `tags.tag-name`, ponieważ niektóre znaki nie są poprawnie analizowane w notacji kropkowej.

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

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>Interfejs API REST

Azure Portal i program PowerShell używają [interfejsu API REST Menedżer zasobów](/rest/api/resources/) w tle. Jeśli trzeba zintegrować znakowanie w innym środowisku, można uzyskać Tagi przy użyciu funkcji **Get** na identyfikatorze zasobu i zaktualizować zestaw tagów przy użyciu wywołania **patch** .

## <a name="tags-and-billing"></a>Znaczniki i rozliczanie

Za pomocą tagów można grupować dane dotyczące rozliczeń. Jeśli na przykład używasz wielu maszyn wirtualnych dla różnych organizacji, użyj tagów, aby grupować użycie według centrum kosztów. Za pomocą tagów można również klasyfikować koszty według środowiska uruchomieniowego, takich jak użycie rozliczeń dla maszyn wirtualnych działających w środowisku produkcyjnym.

Informacje o tagach można uzyskać za pomocą [interfejsów API użycia zasobów platformy Azure i RateCard](../../billing/billing-usage-rate-card-overview.md) lub pliku z wartościami rozdzielanymi przecinkami (CSV). Plik użycia można pobrać z [centrum konta platformy Azure](https://account.azure.com/Subscriptions) lub Azure Portal. Aby uzyskać więcej informacji, zobacz [pobieranie lub wyświetlanie faktury rozliczeń na platformie Azure oraz danych dziennego użycia](../../billing/billing-download-azure-invoice-daily-usage-date.md). Podczas pobierania pliku użycia z Centrum konta platformy Azure wybierz pozycję **wersja 2**. W przypadku usług, które obsługują Tagi z rozliczeniami, Tagi są wyświetlane w kolumnie **Tagi** .

Aby uzyskać informacje na temat operacji interfejsu API REST, zobacz [Dokumentacja interfejsu API REST rozliczeń platformy Azure](/rest/api/billing/).

## <a name="next-steps"></a>Następne kroki

* Nie wszystkie typy zasobów obsługują Tagi. Aby określić, czy można zastosować tag do typu zasobu, zobacz [obsługa tagów dla zasobów platformy Azure](tag-support.md).
* Aby zapoznać się z wprowadzeniem do korzystania z portalu, zobacz [używanie Azure Portal do zarządzania zasobami platformy Azure](manage-resource-groups-portal.md).  
