---
title: Ochrona prywatnych stref i rekordów DNS — usługa Azure DNS
description: W tej ścieżce szkoleniowej rozpocznij ochronę prywatnych stref DNS i zestawów rekordów w usłudze Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: da94c9aa97483ab5792e917d6a8f60f846b0722e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473066"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>Jak chronić prywatne strefy i rekordy DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prywatne strefy i rekordy DNS są zasobami krytycznymi. Usunięcie strefy DNS lub pojedynczego rekordu DNS może spowodować awarię usługi. Ważne jest, aby strefy i rekordy DNS były chronione przed nieautoryzowanymi lub przypadkowymi zmianami.

W tym artykule wyjaśniono, jak usługa Azure DNS umożliwia ochronę prywatnych stref DNS i rekordów przed takimi zmianami.  Stosujemy dwie zaawansowane funkcje zabezpieczeń udostępniane przez usługę Azure Resource Manager: [kontrolę dostępu opartą na rolach](../role-based-access-control/overview.md) i [blokady zasobów.](../azure-resource-manager/management/lock-resources.md)

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Kontrola dostępu oparta na rolach platformy Azure (RBAC) umożliwia szczegółowe zarządzanie dostępem dla użytkowników, grup i zasobów platformy Azure. Z RBAC, można udzielić poziomu dostępu, który użytkownicy potrzebują. Aby uzyskać więcej informacji o tym, jak RBAC pomaga zarządzać dostępem, zobacz [Co to jest kontrola dostępu oparta na rolach](../role-based-access-control/overview.md).

### <a name="the-private-dns-zone-contributor-role"></a>Rola Współautor strefy DNS prywatnej

Rola Współtwórca strefy DNS prywatnej jest wbudowaną rolą zarządzania prywatnymi zasobami DNS. Ta rola zastosowana do użytkownika lub grupy umożliwia mu zarządzanie prywatnymi zasobami DNS.

Grupa zasobów *myPrivateDNS* zawiera pięć stref dla contoso corporation. Przyznanie administratorowi DNS uprawnień prywatnego współautora strefy DNS do tej grupy zasobów umożliwia pełną kontrolę nad tymi strefami DNS. Pozwala uniknąć udzielania niepotrzebnych uprawnień. Administrator DNS nie może tworzyć ani zatrzymywać maszyn wirtualnych.

Najprostszym sposobem przypisywania uprawnień RBAC jest [za pośrednictwem witryny Azure portal](../role-based-access-control/role-assignments-portal.md).  

**Open Access Control (IAM)** dla grupy zasobów, wybierz pozycję **Dodaj**, a następnie wybierz rolę Współautor **strefy DNS.** Wybierz wymaganych użytkowników lub grupy, aby udzielić uprawnień.

![Rbac na poziomie grupy zasobów za pośrednictwem witryny Azure portal](./media/dns-protect-private-zones-recordsets/rbac1.png)

Uprawnienia można również [przyznać za pomocą programu Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Równoważne polecenie jest również [dostępne za pośrednictwem interfejsu wiersza polecenia platformy Azure:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-rbac"></a>Poziom strefy prywatnej RBAC

Reguły rbac platformy Azure mogą być stosowane do subskrypcji, grupy zasobów lub do pojedynczego zasobu. Zasób ten może być pojedynczą strefą DNS lub pojedynczym zestawem rekordów.

Na przykład grupa zasobów *myPrivateDNS* zawiera *private.contoso.com* strefy i *customers.private.contoso.com*podstrefy . Rekordy CNAME są tworzone dla każdego konta odbiorcy. Konto administratora używane do zarządzania rekordami CNAME ma przypisane uprawnienia do tworzenia rekordów w strefie *customers.private.contoso.com.* Konto może zarządzać tylko *customers.private.contoso.com.*

Uprawnienia RBAC na poziomie strefy mogą być przyznawane za pośrednictwem witryny Azure portal.  Otwórz **formant dostępu (IAM)** dla strefy, wybierz pozycję **Dodaj**, a następnie wybierz rolę Współautor **strefy DNS.** Wybierz wymaganych użytkowników lub grupy, aby udzielić uprawnień.

![RBAC na poziomie strefy DNS za pośrednictwem portalu Azure](./media/dns-protect-private-zones-recordsets/rbac2.png)

Uprawnienia można również [przyznać za pomocą programu Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

Równoważne polecenie jest również [dostępne za pośrednictwem interfejsu wiersza polecenia platformy Azure:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>Poziom zestawu rekordów RBAC

Uprawnienia są stosowane na poziomie zestawu rekordów.  Użytkownik otrzymuje kontrolę nad wpisami, których potrzebują i nie może wprowadzać żadnych innych zmian.

Uprawnienia RBAC na poziomie rekordu można skonfigurować za pośrednictwem witryny Azure portal, używając przycisku **Kontrola dostępu (IAM)** na stronie zestawu rekordów:

![Poziom rekordu RBAC za pośrednictwem portalu Azure](./media/dns-protect-private-zones-recordsets/rbac3.png)

![Poziom rekordu RBAC za pośrednictwem portalu Azure](./media/dns-protect-private-zones-recordsets/rbac4.png)

Uprawnienia RBAC na poziomie rekordu można również [przyznać za pomocą programu Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Równoważne polecenie jest również [dostępne za pośrednictwem interfejsu wiersza polecenia platformy Azure:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Role niestandardowe

Wbudowana rola prywatnego współautora strefy DNS umożliwia pełną kontrolę nad zasobem DNS. Możliwe jest tworzenie własnych niestandardowych ról platformy Azure, aby zapewnić lepszą kontrolę.

Konto, które jest używane do zarządzania CNAMEs ma uprawnienia do zarządzania rekordami CNAME tylko. Konto nie może modyfikować rekordów innych typów. Konto nie może wykonywać operacji na poziomie strefy, takich jak usuwanie strefy.

W poniższym przykładzie przedstawiono niestandardową definicję roli do zarządzania rekordami CNAME tylko:

```json
{
    "Name": "Private DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/privateDnsZones/CNAME/*",
        "Microsoft.Network/privateDNSZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

Właściwość Akcje definiuje następujące uprawnienia specyficzne dla systemu DNS:

* `Microsoft.Network/privateDnsZones/CNAME/*`zapewnia pełną kontrolę nad rekordami CNAME
* `Microsoft.Network/privateDNSZones/read`udziela uprawnień do odczytu stref prywatnych DNS, ale nie do ich modyfikowania, co umożliwia wyświetlenie strefy, w której tworzona jest CNAME.

> [!NOTE]
> Używanie niestandardowej roli RBAC, aby zapobiec usuwaniu zestawów rekordów, a jednocześnie zezwalać na ich aktualizowanie, nie jest skuteczną kontrolą. Zapobiega to usuwaniu zestawów rekordów, ale nie uniemożliwia ich modyfikowanie.  Dozwolone modyfikacje obejmują dodawanie i usuwanie rekordów z zestawu rekordów, w tym usuwanie wszystkich rekordów w celu pozostawienia pustego zestawu rekordów. Ma to taki sam efekt jak usunięcie rekordu ustawionego z punktu widzenia rozpoznawania DNS.

Niestandardowe definicje ról nie można obecnie zdefiniować za pośrednictwem witryny Azure portal. Rolę niestandardową opartą na tej definicji roli można utworzyć przy użyciu programu Azure PowerShell:

```azurepowershell-interactive
# Create new role definition based on input file

New-AzRoleDefinition -InputFile <file path>
```

Można go również utworzyć za pośrednictwem interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
# Create new role definition based on input file

az role create -inputfile <file path>
```

Rolę można następnie przypisać w taki sam sposób jak wbudowane role, jak opisano wcześniej w tym artykule.

Aby uzyskać więcej informacji na temat tworzenia ról niestandardowych, zarządzania nimi i przypisywania ich, zobacz [Role niestandardowe w usłudze Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Blokady zasobów

Usługa Azure Resource Manager obsługuje inny typ kontroli zabezpieczeń, możliwość blokowania zasobów. Blokady zasobów są stosowane do zasobu i są skuteczne dla wszystkich użytkowników i ról. Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](../azure-resource-manager/management/lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

Istnieją dwa typy blokady zasobów: **CanNotDelete** i **ReadOnly**. Te typy blokad mogą być stosowane do prywatnej strefy DNS lub do pojedynczego zestawu rekordów.  W poniższych sekcjach opisano kilka typowych scenariuszy i jak obsługiwać je przy użyciu blokad zasobów.

### <a name="protecting-against-all-changes"></a>Ochrona przed wszelkimi zmianami

Aby zapobiec wystosowaniu zmian, należy zastosować blokadę tylko do odczytu do strefy. Ta blokada zapobiega tworzenia nowych zestawów rekordów, a istniejące zestawy rekordów są modyfikowane lub usuwane.

Blokady zasobów na poziomie strefy można utworzyć za pośrednictwem witryny Azure portal.  Na stronie strefa DNS wybierz pozycję **Blokady**, a następnie wybierz pozycję **+Dodaj**:

![Blokady zasobów na poziomie strefy za pośrednictwem witryny Azure portal](./media/dns-protect-private-zones-recordsets/locks1.png)

Blokady zasobów na poziomie strefy można również tworzyć za pośrednictwem [programu Azure PowerShell:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest)

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Równoważne polecenie jest również [dostępne za pośrednictwem interfejsu wiersza polecenia platformy Azure:](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create)

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "privateDnsZones" \
--resource-group "<resource group name>"
```
### <a name="protecting-individual-records"></a>Ochrona poszczególnych rekordów

Aby zapobiec istniejącemu zestawowi rekordów DNS względem modyfikacji, zastosuj blokadę tylko do odczytu do zestawu rekordów.

> [!NOTE]
> Zastosowanie blokady CanNotDelete do zestawu rekordów nie jest skuteczną kontrolą. Zapobiega to usunięciu zestawu rekordów, ale nie uniemożliwia jego modyfikowanie.  Dozwolone modyfikacje obejmują dodawanie i usuwanie rekordów z zestawu rekordów, w tym usuwanie wszystkich rekordów w celu pozostawienia pustego zestawu rekordów. Ma to taki sam efekt jak usunięcie rekordu ustawionego z punktu widzenia rozpoznawania DNS.

Blokady zasobów zestawu rekordów można obecnie skonfigurować tylko przy użyciu programu Azure PowerShell.  Nie są one obsługiwane w witrynie Azure portal ani w witrynie Azure CLI.

Azure PowerShell

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rnm = "<zone name>/<record set name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
### <a name="protecting-against-zone-deletion"></a>Ochrona przed usuwaniem strefy

Po usunięciu strefy w usłudze Azure DNS wszystkie zestawy rekordów w strefie są usuwane.  Tej operacji nie można cofnąć. Przypadkowe usunięcie strefy krytycznej może mieć znaczący wpływ na działalność.  Ważne jest, aby chronić przed przypadkowym usunięciem strefy.

Zastosowanie blokady CanNotDelete do strefy zapobiega usunięciu strefy. Blokady są dziedziczone przez zasoby podrzędne. Blokada zapobiega usuwaniu wszystkich zestawów rekordów w strefie. Jak opisano w powyższej uwadze, jest to nieskuteczne, ponieważ rekordy nadal można usunąć z istniejących zestawów rekordów.

Alternatywnie zastosuj blokadę CanNotDelete do rekordu ustawionego w strefie, takiego jak zestaw rekordów SOA. Strefa nie jest usuwana bez usuwania zestawów rekordów. Ta blokada chroni przed usuwaniem strefy, jednocześnie umożliwiając swobodną modyfikację zestawów rekordów w strefie. Jeśli zostanie podjęta próba usunięcia strefy, usługa Azure Resource Manager wykryje to usunięcie. Usunięcie spowoduje również usunięcie zestawu rekordów SOA, usługa Azure Resource Manager blokuje wywołanie, ponieważ soa jest zablokowany.  Żadne zestawy rekordów nie są usuwane.

Następujące polecenie programu PowerShell tworzy blokadę CanNotDelete względem rekordu SOA danej strefy:

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
Inną opcją zapobiegania przypadkowemu usunięciu strefy jest użycie roli niestandardowej. Ta rola gwarantuje, że konta używane do zarządzania strefami nie mają uprawnień do usuwania stref. 

Jeśli chcesz usunąć strefę, możesz wymusić usunięcie dwuetapowe:

 - Najpierw przyznaj uprawnienia do usuwania strefy
 - Po drugie, przyznaj uprawnienia do usuwania strefy.

Rola niestandardowa działa dla wszystkich stref, do które mają dostęp te konta. Konta z uprawnieniami do usuwania strefy, takie jak właściciel subskrypcji, nadal mogą przypadkowo usunąć strefę.

Istnieje możliwość użycia obu podejść — blokad zasobów i ról niestandardowych — w tym samym czasie, jako obrony w głębi podejścia do ochrony strefy DNS.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat pracy z RBAC, zobacz [Wprowadzenie do zarządzania dostępem w witrynie Azure portal](../role-based-access-control/overview.md).
* Aby uzyskać więcej informacji na temat pracy z blokadami zasobów, zobacz [Blokowanie zasobów za pomocą usługi Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
