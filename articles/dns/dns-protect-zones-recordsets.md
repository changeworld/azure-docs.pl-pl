---
title: Ochrona usługi Azure strefy i rekordy DNS
description: Jak chronić strefy DNS i zestawami rekordów w systemie Microsoft Azure DNS.
services: dns
author: WenJason
ms.service: dns
ms.topic: article
origin.date: 12/4/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: 9340a43eb88b4be03c0f0ccc0d07a32f22a9001c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66121452"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Jak chronić strefy i rekordy DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Strefy i rekordy DNS są zasoby o znaczeniu krytycznym. Usunięcie strefy DNS lub nawet tylko jeden rekord DNS może spowodować awarię łączna liczba usług.  Dlatego ważne jest krytyczne strefy i rekordy DNS są chronione przed nieautoryzowanych lub przypadkowych zmian.

W tym artykule wyjaśniono, jak usługi Azure DNS pozwala chronić swoje strefy i rekordy DNS dla takich zmian.  Możemy zastosować dwa zaawansowanych funkcji zabezpieczeń udostępniane przez usługi Azure Resource Manager: [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) i [blokad zasobów](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="role-based-access-control"></a>Kontrola dostępu na podstawie ról

Usługa Azure opartej na rolach kontrola dostępu (RBAC) umożliwia precyzyjne zarządzanie dostępem dla platformy Azure użytkownikom, grupom i zasobów. Korzystając z modelu RBAC, można przyznać dokładnie ilość dostępu potrzebnym użytkownikom do wykonywania swoich zadań. Aby uzyskać więcej informacji na temat sposobu RBAC ułatwia zarządzanie dostępem, zobacz [co to jest kontrola dostępu oparta na rolach](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>Rola Współautor strefy DNS

Rola Współautor strefy DNS jest wbudowana rola udostępnianych przez platformę Azure do zarządzania zasobami DNS.  Przypisywanie uprawnień Współautor strefy DNS użytkownikowi lub grupie umożliwia tę grupę do zarządzania zasobami DNS, ale nie zasobów innego typu.

Na przykład, załóżmy, że grupa zasobów *myzones* zawiera pięć stref dla Contoso Corporation. Udzielenie DNS administrator uprawnienia Współautor strefy DNS w tej grupie zasobów, umożliwia pełną kontrolę nad tych stref DNS. Unika również udzielanie niepotrzebnych uprawnień, na przykład administrator DNS nie można utworzyć ani zatrzymywać maszyny wirtualne.

Najprostszym sposobem, aby przypisać uprawnienia RBAC jest [za pośrednictwem witryny Azure portal](../role-based-access-control/role-assignments-portal.md).  Otwórz **kontrola dostępu (IAM)** dla grupy zasobów, następnie wybierz pozycję **Dodaj**, a następnie wybierz **Współautor strefy DNS** rolę i wybierz wymaganych użytkowników lub grupy, aby udzielić uprawnienia.

![RBAC poziomu grupy zasobów w witrynie Azure portal](./media/dns-protect-zones-recordsets/rbac1.png)

Uprawnienia można też [przyznać za pomocą programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

Równoważne polecenia jest także [dostępne za pośrednictwem wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>Poziomu strefy RBAC

Reguły usługi Azure RBAC można zastosować do subskrypcji, grupy zasobów lub poszczególnych zasobów. W przypadku usługi Azure DNS tego zasobu może być poszczególnych stref DNS lub nawet poszczególnych zestawu rekordów.

Na przykład, załóżmy, że grupa zasobów *myzones* zawiera strefę *contoso.com* i subzone *customers.contoso.com* w CNAME, które zostały utworzone rekordy dla każdego Konto klienta.  Konto używane do zarządzania tymi rekordami CNAME należy przypisać uprawnienia do tworzenia rekordów w *customers.contoso.com* tylko strefy, nie powinny mieć dostępu do innych strefach.

W witrynie Azure portal można udzielić uprawnień RBAC poziomu strefy.  Otwórz **kontrola dostępu (IAM)** strefy, następnie wybierz **Dodaj**, a następnie wybierz **Współautor strefy DNS** rolę i wybierz wymaganych użytkowników lub grupy, aby udzielić uprawnień.

![RBAC poziomu strefy DNS w witrynie Azure portal](./media/dns-protect-zones-recordsets/rbac2.png)

Uprawnienia można też [przyznać za pomocą programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

Równoważne polecenia jest także [dostępne za pośrednictwem wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>Poziom RBAC zestawu rekordów

Firma Microsoft wykonaj krok dalej. Należy wziąć pod uwagę administratora poczty dla Contoso Corporation, który musi mieć dostęp do rekordów MX i TXT w wierzchołku strefy contoso.com.  Nie potrzebuje dostępu do innych rekordów MX lub TXT lub wszystkie rekordy innego typu.  Usługa system DNS Azure umożliwia przypisywanie uprawnień na poziomie zestawu rekordów do dokładnie te rekordy, które administrator poczty musi mieć dostęp do.  Administratora wiadomości e-mail będzie udzielany precyzyjnie kontrolować potrzebuje i nie będzie mógł wprowadzić inne zmiany.

Zestaw rekordów poziomu uprawnień RBAC można skonfigurować w witrynie Azure portal, za pomocą **użytkowników** przycisk na stronie zestaw rekordów:

![Zestaw rekordów poziom kontroli RBAC przy użyciu witryny Azure portal](./media/dns-protect-zones-recordsets/rbac3.png)

Zestaw rekordów poziomu uprawnień RBAC można też [przyznać za pomocą programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

Równoważne polecenia jest także [dostępne za pośrednictwem wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Role niestandardowe

Wbudowana rola Współautor strefy DNS umożliwia pełną kontrolę nad zasobów DNS. Istnieje również możliwość tworzenia własnych klientów ról platformy Azure, aby zapewnić jeszcze bardziej szczegółowej kontroli.

Należy wziąć pod uwagę ponownie przykład rejestrowania rekord CNAME w strefie *customers.contoso.com* jest tworzony dla każdego konta klienta, firma Contoso.  Konto używane do zarządzania tych rekordów CNAME powinien mieć uprawnienie do zarządzania tylko rekordy CNAME.  Następnie nie może modyfikować rekordy innych typów (na przykład zmiana rekordów MX) lub wykonywać operacje poziomu strefy, takie jak usuwanie strefy.

Poniższy przykład przedstawia niestandardową definicję roli do zarządzania tylko rekordy CNAME:

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
        "Microsoft.Resources/subscriptions/resourceGroups/read"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

Właściwość akcji definiuje następujące uprawnienia dotyczące DNS:

* `Microsoft.Network/dnsZones/CNAME/*` przyznaje pełną kontrolę nad rekordy CNAME
* `Microsoft.Network/dnsZones/read` udziela uprawnień do odczytu stref DNS, ale nie należy modyfikować je, dzięki którym można zobaczyć strefy, w której jest tworzony rekord CNAME.

Pozostałe operacje są kopiowane z [wbudowana rola Współautor strefy DNS](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Za pomocą niestandardową rolę RBAC, aby uniemożliwić usunięcie zestawów rekordów, chociaż nadal umożliwia im do zaktualizowania nie efektywną kontrolę. Uniemożliwia zestawy rekordów usuwany, ale nie uniemożliwia ich przed modyfikacją.  Dozwolone modyfikacje obejmują dodawanie i usuwanie rekordów z zestawu rekordów, łącznie z usunięciem wszystkich rekordów, aby pozostawić pusty zestaw rekordów. Ma to taki sam skutek jak usuwanie zestawu z punktu widzenia rozpoznawania DNS rekordów.

Definicji ról niestandardowych obecnie nie można zdefiniować za pośrednictwem witryny Azure portal. Można utworzyć rolę niestandardową, w oparciu o tę definicję roli przy użyciu programu Azure PowerShell:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Jego można również utworzyć za pomocą wiersza polecenia platformy Azure:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

Rolę można przypisać w taki sam sposób jak wbudowanych ról, zgodnie z opisem we wcześniejszej części tego artykułu.

Aby uzyskać więcej informacji na temat sposobu tworzenia, zarządzania i przypisywanie ról niestandardowych, zobacz [niestandardowych ról RBAC platformy Azure](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Blokady zasobów

Oprócz RBAC usługi Azure Resource Manager obsługuje innego typu kontroli zabezpieczeń, a mianowicie możliwość blokowanie zasobów. Gdzie RBAC, zasady umożliwiają kontrolowanie działania konkretnych użytkowników i grup blokad zasobów są stosowane do zasobu i obowiązują we wszystkich użytkowników i ról. Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

Istnieją dwa rodzaje blokady zasobu: **CanNotDelete** i **tylko do odczytu**. Te można zastosować do strefy DNS lub do poszczególnych zestawu rekordów.  W poniższych sekcjach opisano kilka typowych scenariuszy oraz sposób ich obsługi przy użyciu blokad zasobów.

### <a name="protecting-against-all-changes"></a>Ochrona przed wszystkie zmiany

Aby uniknąć jakichkolwiek zmian, należy zastosować blokadę tylko do odczytu do strefy.  Zapobiega to jest utworzony i istniejących zestawów rekordów, zmodyfikowane lub usunięte nowe zestawy rekordów.

Blokad zasobów na poziomie strefy można tworzyć za pomocą witryny Azure portal.  Na stronie strefa DNS, wybierz **blokuje**, a następnie wybierz **+ Dodaj**:

![Blokad zasobów na poziomie strefy za pośrednictwem witryny Azure portal](./media/dns-protect-zones-recordsets/locks1.png)

Poziomu strefy zasób, który blokad można również utworzyć za pomocą programu Azure PowerShell:

```azurepowershell
# Lock a DNS zone
New-AzResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Konfigurowanie blokad zasobów platformy Azure nie jest obecnie obsługiwane za pośrednictwem wiersza polecenia platformy Azure.

### <a name="protecting-individual-records"></a>Ochrona poszczególnych rekordów

Aby zapobiec ustawić przed zmianami istniejącego rekordu DNS, należy zastosować blokadę tylko do odczytu do zestawu rekordów.

> [!NOTE]
> Stosowanie blokady CanNotDelete do zestawu rekordów nie jest efektywną kontrolę. Uniemożliwia ona zestawu przed usunięciem rekordów, ale go nie zapobiega on modyfikowany.  Dozwolone modyfikacje obejmują dodawanie i usuwanie rekordów z zestawu rekordów, łącznie z usunięciem wszystkich rekordów, aby pozostawić pusty zestaw rekordów. Ma to taki sam skutek jak usuwanie zestawu z punktu widzenia rozpoznawania DNS rekordów.

Blokad zasobów na poziomie zestawu rekordów można obecnie tylko można skonfigurować za pomocą programu Azure PowerShell.  Nie są obsługiwane w witrynie Azure portal lub interfejsu wiersza polecenia platformy Azure.

```azurepowershell
# Lock a DNS record set
New-AzResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Ochrona przed usunięciem strefy

Po usunięciu strefy w usłudze Azure DNS wszystkich zestawów rekordów w strefie również zostaną usunięte.  Tej operacji nie można cofnąć.  Przypadkowemu usunięciu strefy krytyczne ma potencjalnie znaczny wpływ na działalność.  W związku z tym jest bardzo ważne zapewnić ochronę przed zapobiec przypadkowemu usunięciu strefy.

Stosowanie blokady CanNotDelete do strefy uniemożliwia usunięcie strefy.  Jednak ponieważ blokady są dziedziczone przez zasoby podrzędne, uniemożliwia także żadnych zestawów rekordów w strefie przed usunięciem, który może mieć niepożądane.  Ponadto zgodnie z opisem w powyższej Uwaga, jest również nieskuteczne od rekordów, nadal można je usunąć z istniejących zestawów rekordów.

Jako alternatywę należy rozważyć stosowanie blokady CanNotDelete do zestaw rekordów w strefie, takiego jak zestaw rekordów SOA.  Ponieważ nie można usunąć strefy bez usuwania również zestawów rekordów, chroni to przed usunięciem strefy, umożliwiając jednocześnie zestawów rekordów wewnątrz strefy można swobodnie modyfikować. Jeśli zostanie podjęta próba usunąć strefę, usługi Azure Resource Manager wykrywa to spowoduje również usunięcie zestawu rekordów SOA i blokuje połączenia, ponieważ SOA jest zablokowany.  Nie zestawów rekordów są usuwane.

Następujące polecenie programu PowerShell tworzy CanNotDelete blokadą rekord SOA dla określonej strefy:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set
New-AzResourceLock -LockLevel CanNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Innym sposobem uniknięcia zapobiec przypadkowemu usunięciu strefy jest przy użyciu rolę niestandardową, aby upewnić się, operator i konta usług używane do zarządzania strefami nie mają strefy Usuń uprawnienia. Musisz usunąć strefę, można wymusić delete dwuetapowej, pierwszy musi udzielać strefy uprawnienia do usuwania (w zakresie strefy, aby uniemożliwić usunięcie nieprawidłową strefę) i sekundy można usunąć strefy.

To drugie podejście ma tę zaletę, że działa we wszystkich strefach uzyskiwał dostęp do tych kont bez konieczności Pamiętaj, aby utworzyć żadnych blokad. Ma ona wadą, czy wszystkie konta z uprawnieniami do usunięcia strefy, takich jak właściciel subskrypcji może nadal przypadkowo usuniesz krytyczne strefy.

Istnieje możliwość używania obu metod — blokad zasobów i role niestandardowe — w tym samym czasie, ochronę w głębi sposobem ochrony strefy DNS.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat pracy z RBAC, zobacz [wprowadzenie do zarządzania dostępem w witrynie Azure portal](../role-based-access-control/overview.md).
* Aby uzyskać więcej informacji na temat pracy z blokad zasobów, zobacz [blokowanie zasobów w usłudze Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).
