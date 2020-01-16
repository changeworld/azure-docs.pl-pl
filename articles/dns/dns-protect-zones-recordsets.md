---
title: Ochrona Strefy DNS i rekordów — Azure DNS
description: W tej ścieżce szkoleniowej Rozpocznij ochronę stref DNS i zestawów rekordów w Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 12/4/2018
ms.author: allensu
ms.openlocfilehash: c87f9d51c69c4f4d330862e83e5cc8e8e849a988
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969023"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Jak chronić strefy i rekordy DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Strefy i rekordy DNS są istotnymi zasobami. Usunięcie strefy DNS lub nawet pojedynczego rekordu DNS może spowodować całkowitą awarię usługi.  Należy zatem pamiętać, że krytyczne strefy i rekordy DNS są chronione przed nieautoryzowanymi lub przypadkowymi zmianami.

W tym artykule wyjaśniono, jak Azure DNS pozwala chronić strefy i rekordy DNS przed takimi zmianami.  Stosujemy dwie zaawansowane funkcje zabezpieczeń zapewniane przez Azure Resource Manager: [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) i [blokad zasobów](../azure-resource-manager/management/lock-resources.md).

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Access Control oparte na rolach (RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem użytkowników, grup i zasobów platformy Azure. Korzystając z funkcji RBAC, można udzielić dokładnej ilości dostępu, którą użytkownicy potrzebują do wykonywania swoich zadań. Aby uzyskać więcej informacji o sposobach zarządzania dostępem przez funkcję RBAC, zobacz [co to jest Access Control oparte na rolach](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>Rola współautor strefy DNS

Rola współautor strefy DNS to wbudowana rola udostępniana przez platformę Azure do zarządzania zasobami DNS.  Przypisanie uprawnień współautora strefy DNS do użytkownika lub grupy umożliwia tej grupie Zarządzanie zasobami DNS, ale nie zasobów dowolnego innego typu.

Załóżmy na przykład, że moja *strefa* grupy zasobów zawiera pięć stref dla firmy Contoso Corporation. Przyznanie uprawnienia współautora strefy DNS administratora DNS tej grupie zasobów umożliwia pełną kontrolę nad tymi strefami DNS. Pozwala również uniknąć udzielania niepotrzebnych uprawnień, na przykład administrator DNS nie może utworzyć ani zatrzymać Virtual Machines.

Najprostszym sposobem przypisywania uprawnień RBAC jest [za pośrednictwem Azure Portal](../role-based-access-control/role-assignments-portal.md).  Otwórz przystawkę **Kontrola dostępu (IAM)** dla grupy zasobów, a następnie wybierz pozycję **Dodaj**, a następnie wybierz rolę **współautor strefy DNS** i wybierz wymaganych użytkowników lub grupy do udzielenia uprawnień.

![Kontrola RBAC na poziomie grupy zasobów za pomocą Azure Portal](./media/dns-protect-zones-recordsets/rbac1.png)

Uprawnienia można także [przyznawać przy użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

Równoważne polecenie jest również [dostępne za pośrednictwem interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>Kontrola RBAC na poziomie strefy

Reguły RBAC platformy Azure mogą być stosowane do subskrypcji, grupy zasobów lub pojedynczego zasobu. W przypadku Azure DNS ten zasób może być pojedynczą strefą DNS, a nawet pojedynczym zestawem rekordów.

Załóżmy na przykład, że moja *strefa* grupy zasobów zawiera strefę *contoso.com* i podstrefę *Customers.contoso.com* , w której tworzone są rekordy CNAME dla każdego konta klienta.  Konto używane do zarządzania tymi rekordami CNAME powinno mieć przypisane uprawnienia do tworzenia rekordów tylko w strefie *Customers.contoso.com* , nie powinno mieć dostępu do innych stref.

Uprawnienia kontroli RBAC na poziomie strefy można udzielać za pośrednictwem Azure Portal.  Otwórz przystawkę **Kontrola dostępu (IAM)** dla strefy, a następnie wybierz pozycję **Dodaj**, a następnie wybierz rolę **współautor strefy DNS** i wybierz wymaganych użytkowników lub grupy, aby przyznać uprawnienia.

![Kontrola RBAC na poziomie strefy DNS za pośrednictwem Azure Portal](./media/dns-protect-zones-recordsets/rbac2.png)

Uprawnienia można także [przyznawać przy użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

Równoważne polecenie jest również [dostępne za pośrednictwem interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>Poziom zestawu rekordów RBAC

Możemy przejść jeszcze jeden krok. Należy wziąć pod uwagę administratora poczty firmy Contoso Corporation, który potrzebuje dostępu do rekordów MX i TXT na wierzchołku strefy contoso.com.  Nie potrzebuje dostępu do żadnych innych rekordów MX lub TXT ani do żadnych rekordów dowolnego innego typu.  Azure DNS umożliwia przypisywanie uprawnień na poziomie zestawu rekordów do precyzyjnego rejestrowania, do których administrator poczty musi mieć dostęp.  Administrator poczty otrzymuje precyzyjną potrzebę kontroli i nie może wprowadzać żadnych innych zmian.

Uprawnienia RBAC na poziomie rekordu można skonfigurować za pomocą Azure Portal przy użyciu przycisku **Użytkownicy** na stronie zestawu rekordów:

![Ustawianie kontroli RBAC na poziomie rekordu za pośrednictwem Azure Portal](./media/dns-protect-zones-recordsets/rbac3.png)

Na poziomie zestawu rekordów można także udzielić uprawnień RBAC [przy użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

Równoważne polecenie jest również [dostępne za pośrednictwem interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Role niestandardowe

Wbudowana rola współautor strefy DNS umożliwia pełną kontrolę nad zasobem DNS. Istnieje również możliwość utworzenia własnych ról platformy Azure dla klientów, aby zapewnić jeszcze dokładniejszą kontrolę.

Rozważmy ponownie przykład, w którym rekord CNAME w strefie *Customers.contoso.com* jest tworzony dla każdego konta klienta Contoso Corporation.  Konto używane do zarządzania tymi rekordami CNAME powinno mieć przyznane uprawnienie do zarządzania rekordami CNAME.  Nie można wtedy modyfikować rekordów innych typów (takich jak Zmienianie rekordów MX) lub wykonywanie operacji na poziomie strefy, takich jak usuwanie strefy.

Poniższy przykład przedstawia definicję roli niestandardowej do zarządzania tylko rekordami CNAME:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
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
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

Właściwość Actions definiuje następujące uprawnienia specyficzne dla systemu DNS:

* `Microsoft.Network/dnsZones/CNAME/*` przyznaje pełną kontrolę nad rekordami CNAME
* `Microsoft.Network/dnsZones/read` przyznaje uprawnienia do odczytu stref DNS, ale nie zmodyfikuje ich, umożliwiając wyświetlenie strefy, w której jest tworzony rekord CNAME.

Pozostałe akcje są kopiowane z [wbudowanej roli współautor strefy DNS](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Niestandardowa rola RBAC pozwala zapobiegać usuwaniu zestawów rekordów, mimo że nie zezwala na ich aktualizację, nie jest skuteczną kontrolą. Zapobiega to usuwaniu zestawów rekordów, ale nie uniemożliwia ich modyfikacji.  Dozwolone modyfikacje obejmują dodawanie i usuwanie rekordów z zestawu rekordów, włącznie z usunięciem wszystkich rekordów, aby pozostawić pusty zestaw rekordów. Ma to taki sam efekt jak usunięcie zestawu rekordów z punktu widzenia rozpoznawania nazw DNS.

Definicji ról niestandardowych nie można obecnie definiować za pośrednictwem Azure Portal. Rolę niestandardową opartą na tej definicji roli można utworzyć przy użyciu Azure PowerShell:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Można go również utworzyć za pomocą interfejsu wiersza polecenia platformy Azure:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

Rolę można następnie przypisać w taki sam sposób jak wbudowane role, zgodnie z opisem we wcześniejszej części tego artykułu.

Aby uzyskać więcej informacji na temat tworzenia i przypisywania ról niestandardowych oraz zarządzania nimi, zobacz [role niestandardowe w usłudze Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Blokady zasobów

Oprócz RBAC, Azure Resource Manager obsługuje inny typ kontroli zabezpieczeń, a mianowicie możliwość blokowania zasobów. W przypadku, gdy reguły RBAC umożliwiają sterowanie akcjami określonych użytkowników i grup, blokady zasobów są stosowane do zasobu i obowiązują dla wszystkich użytkowników i ról. Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](../azure-resource-manager/management/lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

Istnieją dwa typy blokad zasobów: **CanNotDelete** i **ReadOnly**. Można je zastosować do strefy DNS lub do pojedynczego zestawu rekordów.  W poniższych sekcjach opisano kilka typowych scenariuszy oraz sposób ich obsługi przy użyciu blokad zasobów.

### <a name="protecting-against-all-changes"></a>Ochrona przed wszystkimi zmianami

Aby zapobiec wprowadzeniu zmian, Zastosuj blokadę tylko do odczytu do strefy.  Zapobiega to tworzeniu nowych zestawów rekordów, a istniejące zestawy rekordów można modyfikować lub usuwać.

Blokady zasobów na poziomie strefy można tworzyć za pomocą Azure Portal.  Na stronie strefa DNS wybierz pozycję **blokady**, a następnie wybierz pozycję **+ Dodaj**:

![Blokady zasobów na poziomie strefy za pośrednictwem Azure Portal](./media/dns-protect-zones-recordsets/locks1.png)

Blokady zasobów na poziomie strefy można także utworzyć za pośrednictwem Azure PowerShell:

```azurepowershell
# Lock a DNS zone
New-AzResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Konfigurowanie blokad zasobów platformy Azure nie jest obecnie obsługiwane za pośrednictwem interfejsu wiersza polecenia platformy Azure.

### <a name="protecting-individual-records"></a>Ochrona pojedynczych rekordów

Aby zapobiec modyfikacji istniejącego rekordu DNS, należy zastosować blokadę tylko do odczytu zestawu rekordów.

> [!NOTE]
> Stosowanie blokady CanNotDelete do zestawu rekordów nie jest skuteczną kontrolą. Zapobiega to usuwaniu zestawu rekordów, ale nie uniemożliwia jego modyfikacji.  Dozwolone modyfikacje obejmują dodawanie i usuwanie rekordów z zestawu rekordów, włącznie z usunięciem wszystkich rekordów, aby pozostawić pusty zestaw rekordów. Ma to taki sam efekt jak usunięcie zestawu rekordów z punktu widzenia rozpoznawania nazw DNS.

Blokady zasobów na poziomie zestawu rekordów można obecnie skonfigurować tylko przy użyciu Azure PowerShell.  Nie są one obsługiwane w interfejsie wiersza polecenia Azure Portal ani platformy Azure.

```azurepowershell
# Lock a DNS record set
New-AzResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Ochrona przed usunięciem strefy

Gdy strefa zostanie usunięta w Azure DNS, wszystkie zestawy rekordów w strefie również zostaną usunięte.  Tej operacji nie można cofnąć.  Przypadkowe usunięcie strefy krytycznej może mieć znaczący wpływ na działalność biznesową.  Dlatego bardzo ważne jest, aby chronić przed przypadkowym usunięciem strefy.

Zastosowanie blokady CanNotDelete do strefy uniemożliwia usunięcie strefy.  Jednak ponieważ blokady są dziedziczone przez zasoby podrzędne, również uniemożliwiają usunięcie jakichkolwiek zestawów rekordów w strefie, co może być niepożądane.  Ponadto, zgodnie z opisem w powyższej uwadze, jest również nieskuteczna, ponieważ rekordy nadal mogą być usuwane z istniejących zestawów rekordów.

Alternatywnie rozważ zastosowanie blokady CanNotDelete do zestawu rekordów w strefie, na przykład zestawu rekordów SOA.  Ponieważ nie można usunąć strefy bez również usuwania zestawów rekordów, chroni przed usunięciem strefy, pozostawiając jednocześnie możliwość modyfikacji zestawów rekordów w strefie. Jeśli podjęto próbę usunięcia strefy, Azure Resource Manager wykryje również usunięcie zestawu rekordów SOA i zablokowanie wywołania, ponieważ rekord SOA jest zablokowany.  Żadne zestawy rekordów nie są usuwane.

Następujące polecenie programu PowerShell tworzy blokadę CanNotDelete względem rekordu SOA danej strefy:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set
New-AzResourceLock -LockLevel CanNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Innym sposobem, aby zapobiec przypadkowemu usunięciu strefy jest użycie niestandardowej roli w celu upewnienia się, że operator i konta usług używane do zarządzania strefami nie mają uprawnień do usuwania strefy. Gdy musisz usunąć strefę, możesz wymusić usunięcie dwuetapowe, a najpierw udzielić uprawnień do usuwania strefy (w zakresie strefy, aby zapobiec usunięciu niewłaściwej strefy) i sekundę, aby usunąć strefę.

Ta druga metoda ma zaletę, że działa ona dla wszystkich stref, do których uzyskuje dostęp te konta, bez konieczności tworzenia żadnych blokad. Jest to wadą, że wszystkie konta z uprawnieniami do usuwania strefy, takie jak właściciel subskrypcji, nadal mogą przypadkowo usunąć strefę krytyczną.

Można użyć obu metod — blokad zasobów i ról niestandardowych — w tym samym czasie, co w przypadku podejścia do ochrony strefy DNS.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat pracy z usługą RBAC, zobacz Wprowadzenie do [zarządzania dostępem w Azure Portal](../role-based-access-control/overview.md).
* Aby uzyskać więcej informacji na temat pracy z blokadami zasobów, zobacz [blokowanie zasobów przy użyciu Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
