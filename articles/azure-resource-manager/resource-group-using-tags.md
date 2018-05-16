---
title: Tag zasobów Azure dla organizacji logicznego | Dokumentacja firmy Microsoft
description: Przedstawiono sposób dodawania tagów do organizowania zasobów platformy Azure do rozliczeń i zarządzania nimi.
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
ms.topic: article
ms.date: 01/19/2018
ms.author: tomfitz
ms.openlocfilehash: 5da8c747fb8f89ff627cad74bacf0753bb3484ad
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Organizowanie zasobów platformy Azure przy użyciu tagów

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="powershell"></a>PowerShell

Przykłady w tym artykule wymaga wersji 3.0 lub nowszej programu Azure PowerShell. Jeśli nie masz w wersji 3.0 lub nowszej, [zaktualizuj swoją wersję](/powershell/azureps-cmdlets-docs/) za pomocą Instalatora platformy sieci Web lub w galerii programu PowerShell.

Aby wyświetlić istniejące tagi dla *grupy zasobów*, użyj:

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

Ten skrypt zwraca następujący format:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Aby wyświetlić istniejące tagi dla *zasobu o określonym identyfikatorze zasobu*, użyj:

```powershell
(Get-AzureRmResource -ResourceId {resource-id}).Tags
```

Aby wyświetlić istniejące tagi dla *zasobu o określonej nazwie i grupie zasobów*, użyj:

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Aby uzyskać *grupy zasobów, które mają konkretny tag*, użyj:

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name
```

Aby uzyskać *zasoby, które mają konkretny tag*, użyj:

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

Za każdym razem, gdy stosujesz tagi do zasobu lub grupy zasobów, istniejące tagi tego zasobu lub tej grupy zasobów są zastępowane. Dlatego konieczne jest różne podejście w zależności od tego, czy dany zasób lub dana grupa zasobów ma istniejące tagi.

Aby dodać tagi do *grupy zasobów bez istniejących tagów*, użyj:

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Aby dodać tagi do *grupy zasobów z istniejącymi tagami*, pobierz istniejące tagi, dodaj nowy tag i ponownie zastosuj tagi:

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

Aby dodać tagi do *zasobu bez istniejących tagów*, użyj:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Aby dodać tagi do *zasobu z istniejącymi tagami*, użyj:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.tags += @{Status="Approved"}
Set-AzureRmResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Aby zastosować wszystkie tagi z grupy zasobów do jej zasobów *bez zachowania tagów istniejących w zasobach*, użyj następującego skryptu:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Aby zastosować wszystkie tagi z grupy zasobów do jej zasobów *z zachowaniem tagów istniejących w zasobach, które nie są duplikatami*, użyj następującego skryptu:

```powershell
$group = Get-AzureRmResourceGroup "examplegroup"
if ($group.Tags -ne $null) {
    $resources = $group | Find-AzureRmResource
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        foreach ($key in $group.Tags.Keys)
        {
            if (($resourcetags) -AND ($resourcetags.ContainsKey($key))) { $resourcetags.Remove($key) }
        }
        $resourcetags += $group.Tags
        Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
    }
}
```

Aby usunąć wszystkie tagi, przekaż pustą tablicę skrótów:

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
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

Lub, aby wyświetlić istniejące znaczniki dla *zasób, który ma nazwę, typ i zasobów do określonej grupy*, użyj:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Gdy w pętli kolekcji zasobów, może chcesz pokazać identyfikator zasobu przez zasób. Pełny przykład przedstawiono w dalszej części tego artykułu. Aby wyświetlić istniejące tagi dla *zasobu o określonym identyfikatorze zasobu*, użyj:

```azurecli
az resource show --id <resource-id> --query tags
```

Aby uzyskać grupy zasobów, które mają konkretnego znacznika, należy użyć `az group list`:

```azurecli
az group list --tag Dept=IT
```

Aby uzyskać wszystkie zasoby, które mają określony tag i wartości, należy użyć `az resource list`:

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

Aby dodać tagi do zasobu, który ma już tagi, pobierania istniejące znaczniki, sformatuj ponownie tę wartość i ponownie zastosuj znaczniki istniejących i nowych: 

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

Aby zastosować wszystkie tagi z grupy zasobów do jej zasobów i *zachować istniejące znaczniki zasobów*, użyj następującego skryptu:

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

Portalu Azure i programu PowerShell, oba rozwiązania używają [interfejsu REST API usługi Resource Manager](https://docs.microsoft.com/rest/api/resources/) w tle. Jeśli chcesz zintegrować znakowanie do innego środowiska tagów można uzyskać za pomocą **UZYSKAĆ** na identyfikator zasobu i aktualizacja zestawu tagów za pomocą **poprawka** wywołania.

## <a name="tags-and-billing"></a>Znaczniki i rozliczeń

Tagi służą do grupowania danych rozliczeń. Na przykład jeśli korzystasz z wieloma maszynami wirtualnymi w różnych organizacjach, za pomocą tagów do użycia grup Centrum kosztów. Umożliwia także tagi kategoryzowania koszty przez środowisko uruchomieniowe, takich jak rozliczeń użycia dla maszyn wirtualnych w środowisku produkcyjnym.

Można pobrać informacji na temat tagów za pomocą [użycia zasobów platformy Azure i interfejsów API RateCard](../billing/billing-usage-rate-card-overview.md) lub użycia pliku wartości rozdzielanych przecinkami (CSV). Pobierz plik użycia z [portalu konta usługi Azure](https://account.windowsazure.com/) lub [EA portal](https://ea.azure.com). Aby uzyskać więcej informacji na temat programowy dostęp do informacji dotyczących rozliczeń, zobacz [uzyskać wgląd w Microsoft Azure użycia zasobów](../billing/billing-usage-rate-card-overview.md). Dla operacji interfejsu API REST, zobacz [dokumentacja interfejsu API REST rozliczenia Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Po pobraniu użycia woluminów CSV dla usług, które obsługują tagów z rozliczeniami znaczniki są wyświetlane w **tagi** kolumny. Aby uzyskać więcej informacji, zobacz [zrozumieć rachunku platformy Microsoft Azure](../billing/billing-understand-your-bill.md).

![Zobacz tagów w rozliczeń](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Kolejne kroki

* Za pomocą niestandardowych zasad można stosować ograniczenia i konwencje w Twojej subskrypcji. Zasady, które należy zdefiniować może wymagać, że wszystkie zasoby mają wartość określony tag. Aby uzyskać więcej informacji, zobacz artykuł [Co to jest usługa Azure Policy?](../azure-policy/azure-policy-introduction.md).
* Aby obejrzeć wprowadzenie do korzystania ze środowiska Azure PowerShell podczas wdrażania zasobów, zobacz [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](powershell-azure-resource-manager.md).
* Aby obejrzeć wprowadzenie do przy użyciu wiersza polecenia platformy Azure, podczas wdrażania zasobów, zobacz [przy użyciu wiersza polecenia platformy Azure dla komputerów Mac, Linux i Windows za pomocą Menedżera zasobów Azure](xplat-cli-azure-resource-manager.md).
* Aby obejrzeć wprowadzenie do korzystania z portalu, zobacz [przy użyciu portalu Azure do zarządzania zasobami Azure](resource-group-portal.md).  
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).
