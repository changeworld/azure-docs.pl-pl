---
title: Tagów zasobów platformy Azure dla organizacji logicznego | Dokumentacja firmy Microsoft
description: Pokazuje, jak zastosować znaczniki do organizowania zasobów platformy Azure do rozliczeń i zarządzania nimi.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: tomfitz
ms.openlocfilehash: aa3cd0305c1ac2db269dcc46243ec3da1232e6f6
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2019
ms.locfileid: "55079532"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Organizowanie zasobów platformy Azure przy użyciu tagów

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

Aby zastosować znaczniki do zasobów, użytkownik musi mieć dostęp do zapisu do tego typu zasobu. Aby zastosować znaczniki do wszystkich typów zasobów, użyj [Współautor](../role-based-access-control/built-in-roles.md#contributor) roli. Aby zastosować znaczniki do typu tylko jeden zasób, należy użyć roli współautora dla tego zasobu. Na przykład, aby zastosować znaczniki do maszyn wirtualnych, należy użyć [Współautor maszyny wirtualnej](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="powershell"></a>PowerShell

Przykłady w tym artykule wymagają programu Azure PowerShell w wersji 6.0 lub nowszej. Jeśli nie masz wersji 6.0 lub nowszej, [zaktualizuj swoją wersję](/powershell/azure/install-az-ps).

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

Aby uzyskać *zasoby, które mają konkretny tag nazwę*, użyj:

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

Aby zastosować wszystkie tagi z grupy zasobów do jej zasobów *bez zachowania tagów istniejących w zasobach*, użyj następującego skryptu:

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Aby zastosować wszystkie tagi z grupy zasobów do jej zasobów *z zachowaniem tagów istniejących w zasobach, które nie są duplikatami*, użyj następującego skryptu:

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

Aby wyświetlić istniejące tagi dla *zasób, który ma określoną grupę nazwa, typ i zasobów*, użyj:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Po pętli kolekcją zasobów, możesz chcieć pokazać identyfikator zasobu, zasób Pełny przykład przedstawiono w dalszej części tego artykułu. Aby wyświetlić istniejące tagi dla *zasobu o określonym identyfikatorze zasobu*, użyj:

```azurecli
az resource show --id <resource-id> --query tags
```

Aby uzyskać grupy zasobów, które mają konkretny tag, użyj `az group list`:

```azurecli
az group list --tag Dept=IT
```

Aby uzyskać wszystkie zasoby, które mają konkretny tag i konkretną wartość, użyj `az resource list`:

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

Aby dodać tagi do zasobu, który ma już tagi, Pobierz istniejące tagi, ponownie sformatować tę wartość i ponownie zastosuj tagi istniejące i nowe:

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Aby zastosować wszystkie tagi z grupy zasobów do jej zasobów *bez zachowania tagów istniejących w zasobach*, użyj następującego skryptu:

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

Aby zastosować wszystkie tagi z grupy zasobów do swoich zasobów i *zachowania tagów istniejących w zasobach*, użyj następującego skryptu:

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

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>Interfejs API REST

Witryna Azure portal i programu PowerShell, użyj obu [interfejsu REST API usługi Resource Manager](https://docs.microsoft.com/rest/api/resources/) w tle. Jeśli zachodzi potrzeba integracja znakowanie do innego środowiska, można uzyskać tagów za pomocą **UZYSKAĆ** na identyfikator zasobu i zaktualizuj zestaw znaczników, które przy użyciu **PATCH** wywołania.

## <a name="tags-and-billing"></a>Znaczniki i rozliczenia

Tagi służą do grupowania danych dotyczących rozliczeń. Na przykład jeśli korzystasz z wielu maszyn wirtualnych dla różnych organizacji, należy użyć tagów, aby używanie grupy przez Centrum kosztów. Tagów umożliwia również kategoryzowanie kosztów przez środowisko uruchomieniowe, takie jak rozliczanego użycia dla maszyn wirtualnych uruchomionych w środowisku produkcyjnym.

Można pobrać informacji na temat tagów za pośrednictwem [użycia zasobów platformy Azure i interfejsów API usługi RateCard](../billing/billing-usage-rate-card-overview.md) lub użycie pliku wartości rozdzielanych przecinkami (CSV). Pobieranie pliku użycia z [Centrum konta platformy Azure](https://account.azure.com/Subscriptions) lub witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [pobrania lub wyświetlenia faktury i dane dziennego użycia rozliczeniową za platformę Azure](../billing/billing-download-azure-invoice-daily-usage-date.md). Podczas pobierania pliku użycia z Centrum konta platformy Azure, wybierz **w wersji 2**. W przypadku usług, które obsługują tagi z rozliczeniem znaczniki są wyświetlane w **tagi** kolumny.

Dla operacji interfejsu API REST, zobacz [dokumentacja interfejsu API REST rozliczeń platformy Azure](/rest/api/billing/).

## <a name="next-steps"></a>Kolejne kroki

* Nie wszystkie typy zasobów obsługują tagów. Aby określić, jeśli tag można zastosować do typu zasobu, zobacz [obsługę dla zasobów platformy Azure tagów](tag-support.md).
* Za pomocą zasad niestandardowych, można zastosować ograniczenia i konwencje w ramach subskrypcji. Zasady, które należy zdefiniować może wymagać, że wszystkie zasoby mają wartości dla określonego tagu. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Policy?](../azure-policy/azure-policy-introduction.md)
* Wprowadzenie do korzystania z portalu, zobacz [przy użyciu witryny Azure portal do zarządzania zasobami platformy Azure](resource-group-portal.md).  
