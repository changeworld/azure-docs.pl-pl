---
title: Oznaczanie zasobów, grup zasobów i subskrypcji dla organizacji logicznej
description: Pokazuje, jak stosować tagi do organizowania zasobów platformy Azure do rozliczeń i zarządzania.
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 44759db59ef7e7556c8fbfca3bbc58ef84796198
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010804"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Organizowanie zasobów i hierarchii zarządzania za pomocą tagów

Zastosuj tagi do zasobów platformy Azure, grup zasobów i subskrypcji, aby logicznie zorganizować je w taksonomię. Każdy tag składa się z pary nazw i wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

Aby uzyskać zalecenia dotyczące wdrażania strategii znakowania, zobacz Przewodnik po decyzjach dotyczących [nazewnictwa i oznaczania zasobów](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).

> [!IMPORTANT]
> Nazwy tagów są niewrażliwe na argumenty. W wartościach tagowych rozróżniana jest wielkość liter.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Wymagany dostęp

Aby zastosować znaczniki do zasobu, musisz mieć dostęp do zapisu do typu zasobu **Microsoft.Resources/tags.** Rola [Współautor tagów](../../role-based-access-control/built-in-roles.md#tag-contributor) umożliwia stosowanie tagów do encji bez dostępu do samej jednostki. Obecnie rola współautora tagu nie może stosować tagów do zasobów lub grup zasobów za pośrednictwem portalu. Można zastosować tagi do subskrypcji za pośrednictwem portalu. Obsługuje wszystkie operacje tagów za pośrednictwem programu PowerShell i interfejsu API REST.  

Rola [współautora](../../role-based-access-control/built-in-roles.md#contributor) udziela również wymaganego dostępu do stosowania tagów do dowolnej encji. Aby zastosować znaczniki tylko do jednego typu zasobu, użyj roli współautora dla tego zasobu. Na przykład, aby zastosować znaczniki do maszyn wirtualnych, użyj [współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Stosowanie znaczników

Usługa Azure PowerShell oferuje dwa polecenia do stosowania tagów - [New-AzTag](/powershell/module/az.resources/new-aztag) i [Update-AzTag](/powershell/module/az.resources/update-aztag). Musisz mieć moduł Az.Resources 1.12.0 lub nowszy. Możesz sprawdzić swoją `Get-Module Az.Resources`wersję za pomocą programu . Można zainstalować ten moduł lub [zainstalować program Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1 lub nowszą.

**New-AzTag** zastępuje wszystkie znaczniki w zasobie, grupie zasobów lub subskrypcji. Podczas wywoływania polecenia należy przekazać identyfikator zasobu jednostki, którą chcesz oznaczyć.

Poniższy przykład stosuje zestaw tagów do konta magazynu:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

Po zakończeniu polecenia należy zauważyć, że zasób ma dwa znaczniki.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Jeśli polecenie zostanie uruchomione ponownie, ale tym razem z różnymi tagami, zwróć uwagę, że wcześniejsze tagi zostaną usunięte.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

Aby dodać znaczniki do zasobu, który ma już znaczniki, użyj **funkcji Update-AzTag**. Ustaw parametr **-Operation** na **Scalanie**.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

Należy zauważyć, że dwa nowe tagi zostały dodane do dwóch istniejących tagów.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Każda nazwa tagu może mieć tylko jedną wartość. Jeśli podasz nową wartość dla tagu, stara wartość zostanie zastąpiona, nawet jeśli używasz operacji scalania. W poniższym przykładzie zmienia tag Stan z Normalny na Zielony.

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Po ustawieniu parametru **-Operation** **na Replace**istniejące znaczniki zostaną zastąpione nowym zestawem znaczników.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

Tylko nowe tagi pozostają w zasobie.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Te same polecenia działają również z grupami zasobów lub subskrypcjami. Przekaż identyfikator dla grupy zasobów lub subskrypcji, którą chcesz oznaczyć.

Aby dodać nowy zestaw znaczników do grupy zasobów, użyj:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

Aby zaktualizować znaczniki dla grupy zasobów, użyj:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

Aby dodać nowy zestaw tagów do subskrypcji, użyj:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Aby zaktualizować tagi subskrypcji, użyj:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

W grupie zasobów może znajdować się więcej niż jeden zasób o tej samej nazwie. W takim przypadku można ustawić każdy zasób za pomocą następujących poleceń:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Tworzenie listy tagów

Aby uzyskać tagi dla zasobu, grupy zasobów lub subskrypcji, użyj polecenia [Get-AzTag](/powershell/module/az.resources/get-aztag) i przekaż identyfikator zasobu dla encji.

Aby wyświetlić znaczniki zasobu, użyj:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Aby wyświetlić znaczniki dla grupy zasobów, użyj:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Aby wyświetlić tagi subskrypcji, użyj:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Lista według tagu

Aby uzyskać zasoby, które mają określoną nazwę i wartość tagu, użyj:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Aby uzyskać zasoby, które mają określoną nazwę tagu z dowolną wartością znacznika, użyj:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Aby uzyskać grup zasobów, które mają określoną nazwę i wartość tagu, użyj:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Usuwanie znaczników

Aby usunąć określone znaczniki, użyj **funkcji Update-AzTag** i ustaw **polecenie -Operation** to **Delete**. Przekaż znaczniki, które chcesz usunąć.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

Określone znaczniki zostaną usunięte.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Aby usunąć wszystkie znaczniki, użyj polecenia [Usuń-AzTag.](/powershell/module/az.resources/remove-aztag)

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

### <a name="apply-tags"></a>Stosowanie znaczników

Podczas dodawania znaczników do grupy zasobów lub zasobu można zastąpić istniejące znaczniki lub dołączyć nowe znaczniki do istniejących tagów.

Aby zastąpić znaczniki zasobu, należy użyć:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Aby dołączyć znacznik do istniejących znaczników w zasobie, należy użyć:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Aby zastąpić istniejące znaczniki w grupie zasobów, należy użyć:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Aby dołączyć znacznik do istniejących znaczników w grupie zasobów, należy użyć:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

Obecnie narzędzie interfejsu wiersza polecenia platformy Azure nie obsługuje stosowania tagów do subskrypcji.

### <a name="list-tags"></a>Tworzenie listy tagów

Aby wyświetlić istniejące znaczniki zasobu, użyj:

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Aby wyświetlić istniejące tagi dla grupy zasobów, użyj:

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

### <a name="list-by-tag"></a>Lista według tagu

Aby uzyskać wszystkie zasoby, które mają określony `az resource list`znacznik i wartość, należy użyć:

```azurecli-interactive
az resource list --tag Dept=Finance
```

Aby uzyskać grupy zasobów, które `az group list`mają określony znacznik, użyj:

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>Pomieszczenia przeładunkowe

Jeśli nazwy znaczników lub wartości zawierają spacje, należy wykonać kilka dodatkowych kroków. Poniższy przykład stosuje wszystkie znaczniki z grupy zasobów do jej zasobów, gdy znaczniki mogą zawierać spacje.

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

Zasoby, grupy zasobów i subskrypcje można oznaczyć za pomocą szablonu Menedżera zasobów.

### <a name="apply-values"></a>Stosowanie wartości

W poniższym przykładzie wdraża konto magazynu z trzema tagami. Dwa znaczniki (`Dept` `Environment`i ) są ustawione na wartości literału. Jeden tag`LastDeployed`( ) jest ustawiony na parametr, który domyślnie jest do bieżącej daty.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
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
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
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

### <a name="apply-an-object"></a>Stosowanie obiektu

Możesz zdefiniować parametr obiektu przechowującego kilka tagów i zastosować ten obiekt do elementu tagu. Takie podejście zapewnia większą elastyczność niż w poprzednim przykładzie, ponieważ obiekt może mieć różne właściwości. Każda właściwość obiektu będzie osobnym tagiem dla zasobu. Poniższy przykład zawiera parametr o nazwie `tagValues`, który został zastosowany do elementu tagu.

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

### <a name="apply-a-json-string"></a>Stosowanie ciągu JSON

Aby przechowywać wiele wartości w jednym tagu, zastosuj ciąg JSON reprezentujący te wartości. Cały ciąg JSON jest przechowywany jako jeden znacznik, który nie może przekraczać 256 znaków. Poniższy przykład zawiera pojedynczy tag o nazwie `CostCenter`, który zawiera kilka wartości z ciągu JSON:  

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

### <a name="apply-tags-from-resource-group"></a>Stosowanie znaczników z grupy zasobów

Aby zastosować znaczniki z grupy zasobów do zasobu, użyj funkcji [resourceGroup.](../templates/template-functions-resource.md#resourcegroup) Podczas uzyskiwania wartości znacznika należy użyć `tags[tag-name]` składni zamiast `tags.tag-name` składni, ponieważ niektóre znaki nie są poprawnie analizowane w notacji kropkowej.

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

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Stosowanie znaczników do grup zasobów lub subskrypcji

Tagi można dodawać do grupy zasobów lub subskrypcji, wdrażając typ zasobu **Microsoft.Resources/tags.** Tagi są stosowane do docelowej grupy zasobów lub subskrypcji dla wdrożenia. Za każdym razem, gdy wdrażasz szablon, który zastępujesz wszystkie znaczniki, zostały wcześniej zastosowane.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

Aby zastosować znaczniki do grupy zasobów, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Wdrażanie w grupie zasobów, którą chcesz oznaczyć.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Aby zastosować tagi do subskrypcji, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Wdrażanie w ramach subskrypcji, którą chcesz oznaczyć.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Poniższy szablon dodaje znaczniki z obiektu do grupy zasobów lub subskrypcji.

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>Interfejs API REST

Aby pracować z tagami za pośrednictwem interfejsu API rest platformy Azure, należy użyć:

* [Tagi — tworzenie lub aktualizowanie w zakresie](/rest/api/resources/tags/createorupdateatscope) (operacja PUT)
* [Tagi — aktualizacja w zakresie](/rest/api/resources/tags/updateatscope) (operacja PATCH)
* [Tagi — uzyskaj zakres](/rest/api/resources/tags/getatscope) (operacja GET)
* [Tagi — usuń w zakresie](/rest/api/resources/tags/deleteatscope) (operacja DELETE)

## <a name="inherit-tags"></a>Dziedzicz tagi

Tagi zastosowane do grupy zasobów lub subskrypcji nie są dziedziczone przez zasoby. Aby zastosować tagi z subskrypcji lub grupy zasobów do zasobów, zobacz [Zasady platformy Azure — tagi](tag-policies.md).

## <a name="tags-and-billing"></a>Tagi i rozliczenia

Tagów można użyć do grupowania danych dotyczących rozliczeń. Na przykład jeśli jest uruchomionych wiele maszyn wirtualnych różnych organizacji, możesz użyć tagów do grupowania użycia według centrum kosztu. Tagi umożliwiają również kategoryzowanie kosztów według środowiska uruchomieniowego, na przykład na potrzeby rozliczania użycia maszyn uruchomionych w środowisku produkcyjnym.

Informacje o tagach można pobierać za pośrednictwem interfejsów [API użycia zasobów platformy Azure i ratecard](../../billing/billing-usage-rate-card-overview.md) lub pliku wartości rozdzielanych przecinkami użycia (CSV). Plik użycia można pobrać z [Centrum kont platformy Azure](https://account.azure.com/Subscriptions) lub witryny Azure portal. Aby uzyskać więcej informacji, zobacz [Pobieranie lub wyświetlanie faktury rozliczeniowej platformy Azure i danych dotyczących dziennego użycia](../../billing/billing-download-azure-invoice-daily-usage-date.md). Podczas pobierania pliku użycia z Centrum kont platformy Azure wybierz pozycję **Wersja 2**. W przypadku usług obsługujących tagi z rozliczeniami znaczniki są wyświetlane w kolumnie **Znaczniki.**

Aby zapoznać się z operacjami interfejsu API REST, zobacz [Odwołanie do interfejsu API rest rozliczeń platformy Azure](/rest/api/billing/).

## <a name="limitations"></a>Ograniczenia

Tagi mają następujące ograniczenia:

* Nie wszystkie typy zasobów obsługują tagi. Aby ustalić, czy można zastosować znacznik do typu zasobu, zobacz [Obsługa tagów dla zasobów platformy Azure](tag-support.md).
* Grupy zarządzania obecnie nie obsługują tagów.
* Każdy zasób, grupa zasobów i subskrypcja mogą mieć maksymalnie 50 par nazw/wartości tagów. Jeśli chcesz zastosować więcej tagów niż maksymalna dozwolona liczba, użyj ciągu JSON dla wartości znacznika. Ciąg JSON może zawierać wiele wartości, które są stosowane do jednej nazwy tagu. Grupa zasobów lub subskrypcja może zawierać wiele zasobów, z których każdy ma 50 par nazw tagów/wartości.
* Nazwa tagu może zawierać maksymalnie 512 znaków, a wartość tagu jest ograniczona do 256 znaków. W przypadku kont magazynu nazwa tagu jest ograniczona do 128 znaków, a wartość tagu jest ograniczona do 256 znaków.
* Uogólnione maszyny wirtualne nie obsługują tagów.
* Tagów nie można zastosować do zasobów klasycznych, takich jak usługi w chmurze.
* Nazwy znaczników nie mogą `<`zawierać `>` `%`tych `&` `\`znaków: , , , , , , `?``/`

   > [!NOTE]
   > Obecnie strefy DNS platformy Azure i usługi usługi usługi traffic manger również nie zezwalają na używanie spacji w tagu.

## <a name="next-steps"></a>Następne kroki

* Nie wszystkie typy zasobów obsługują tagi. Aby ustalić, czy można zastosować znacznik do typu zasobu, zobacz [Obsługa tagów dla zasobów platformy Azure](tag-support.md).
* Aby uzyskać zalecenia dotyczące wdrażania strategii znakowania, zobacz Przewodnik po decyzjach dotyczących [nazewnictwa i oznaczania zasobów](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).
