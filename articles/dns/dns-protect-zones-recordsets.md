---
title: Ochrona strefy DNS i rekordy | Dokumentacja firmy Microsoft
description: Jak chronić stref DNS i zestawy rekordów w systemie Microsoft Azure DNS.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 190e69eb-e820-4fc8-8e9a-baaf0b3fb74a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2016
ms.author: kumud
ms.openlocfilehash: d13556eb274990eadf58070c119a3e9960b90699
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="how-to-protect-dns-zones-and-records"></a>Jak chronić stref DNS i rekordów

Strefy DNS i rekordy są kluczowych zasobów. Usunięcie strefy DNS lub nawet tylko pojedynczego rekordu DNS może spowodować awarię usług całkowitej.  Dlatego ważne jest krytyczne strefy DNS i rekordy są chronione przed nieautoryzowanym lub przypadkowe zmiany.

W tym artykule opisano, jak usługi Azure DNS umożliwia ochronę stref DNS oraz rekordów na takie zmiany.  Trwa stosowanie dwie funkcje zaawansowane zabezpieczenia udostępniane przez usługi Azure Resource Manager: [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) i [blokowania zasobów](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Azure opartej na rolach kontroli dostępu (RBAC) umożliwia precyzyjne zarządzanie dostępem dla platformy Azure użytkowników, grup i zasobów. Przy użyciu funkcji RBAC, można przyznać dokładnie takiego dostępu użytkownicy muszą wykonać swoje zadania. Aby uzyskać więcej informacji o sposobie RBAC ułatwia zarządzanie dostępem, zobacz [co to jest kontrola dostępu oparta na rolach](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>Roli "Współautor strefę DNS"

Roli "Współautor strefę DNS" to wbudowana rola dostarczany przez platformę Azure do zarządzania zasobami DNS.  Przypisywanie użytkownikowi lub grupie uprawnienia współautora strefy DNS umożliwia tej grupy do zarządzania zasobów DNS, ale nie zasobów innego typu.

Na przykład załóżmy, że grupa zasobów "myzones" zawiera pięć stref dla Contoso Corporation. Udzielenie DNS administrator "Współautora strefę DNS" uprawnień do tej grupy zasobów, umożliwia pełną kontrolę nad tych stref DNS. Ponadto pozwala uniknąć, udzielanie niepotrzebnych uprawnień, na przykład administrator usługi DNS nie można utworzyć ani zatrzymać maszyn wirtualnych.

Najprostszym sposobem, aby przypisać uprawnienia RBAC jest [za pośrednictwem portalu Azure](../role-based-access-control/role-assignments-portal.md).  Otwarcie bloku "Kontrola dostępu (IAM)" dla grupy zasobów, następnie kliknij przycisk "Dodaj", a następnie wybierz roli "Współautor strefę DNS" i wybierz wymagane użytkowników lub grupy, aby udzielić uprawnień.

![Poziom grupy zasobów RBAC za pośrednictwem portalu Azure](./media/dns-protect-zones-recordsets/rbac1.png)

Uprawnienia można również [przyznane przy użyciu programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

Odpowiednik polecenia jest także [dostępne za pośrednictwem interfejsu wiersza polecenia Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>Poziomu strefy RBAC

Reguły Azure RBAC można zastosować do subskrypcji, grupy zasobów lub do pojedynczego zasobu. W przypadku usługi Azure DNS tego zasobu można poszczególnych stref DNS, lub nawet poszczególnych zestawu rekordów.

Na przykład załóżmy, że grupa zasobów "myzones" zawiera strefy "contoso.com" i subzone "customers.contoso.com" tworzonych rekordy CNAME dla każdego konta klienta.  Konto używane do zarządzania te rekordy CNAME należy przypisać uprawnienia do tworzenia rekordów w strefie "customers.contoso.com" tylko, nie powinny mieć dostępu do innych stref.

Uprawnienia na poziomie strefy RBAC można otrzymać za pośrednictwem portalu Azure.  Otwarcie bloku "Kontrola dostępu (IAM)" dla strefy, kliknij przycisk "Dodaj", a następnie wybierz roli "Współautor strefę DNS" i wybierz wymaganych użytkowników lub grupy, aby udzielić uprawnień.

![Strefa DNS RBAC poziomu portalu Azure](./media/dns-protect-zones-recordsets/rbac2.png)

Uprawnienia można również [przyznane przy użyciu programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

Odpowiednik polecenia jest także [dostępne za pośrednictwem interfejsu wiersza polecenia Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>Poziom RBAC zestawu rekordów

Firma Microsoft wykonaj krok dalej. Należy wziąć pod uwagę administratora poczty dla Contoso Corporation, który wymaga dostępu do rekordów MX i TXT w wierzchołku strefy "contoso.com".  Nie musi ona dostęp do innych rekordów MX lub TXT lub rekordy innego typu.  Usługa Azure DNS umożliwia przypisanie uprawnienia na poziomie zestawu rekordów, aby precyzyjnie rekordy, które administrator poczty musi mieć dostęp do.  Administrator poczty otrzymuje dokładnie kontroli ona należy i nie może wprowadzić inne zmiany.

Zestaw rekordów uprawnień na poziomie RBAC można skonfigurować za pośrednictwem portalu Azure za pomocą przycisku "Użytkowników" w bloku zestawu rekordów:

![Poziom RBAC za pośrednictwem portalu Azure zestawu rekordów](./media/dns-protect-zones-recordsets/rbac3.png)

Zestaw rekordów uprawnień na poziomie RBAC można też [przyznane przy użyciu programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```powershell
# Grant permissions to a specific record set
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

Odpowiednik polecenia jest także [dostępne za pośrednictwem interfejsu wiersza polecenia Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Role niestandardowe

Wbudowane roli "Współautor strefę DNS" umożliwia pełną kontrolę nad zasobów DNS. Istnieje również możliwość tworzenia własnego klienta Azure role, do zapewnienia jeszcze bardziej precyzyjną system kontroli.

Należy wziąć pod uwagę ponownie przykład, w której zostaje utworzony rekord CNAME w strefie "customers.contoso.com" dla każdego konta klienta Contoso Corporation.  Konto używane do zarządzania tymi CNAME powinien mieć uprawnienie do zarządzania tylko rekordy CNAME.  Następnie nie może zmodyfikować rekordy z innych typów (np. zmiana rekordów MX) lub wykonywania operacji na poziomie strefy takich jak usuwanie strefy.

W poniższym przykładzie przedstawiono niestandardową definicję roli do zarządzania tylko rekordy CNAME:

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

Właściwość akcje definiuje następujące uprawnienia dotyczące DNS:

* `Microsoft.Network/dnsZones/CNAME/*` przyznaje pełną kontrolę nad rekordy CNAME
* `Microsoft.Network/dnsZones/read` udziela uprawnień do odczytu stref DNS, ale nie można zmodyfikować, umożliwiając Zobacz strefy, w którym jest tworzona CNAME.

Pozostałe akcje są kopiowane z [wbudowana Rola współautora strefy DNS](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Aby zapobiec usuwanie zestawów rekordów podczas jednocześnie ich aktualizacji nie jest skuteczną kontrolę przy użyciu niestandardowej roli zabezpieczeń RBAC. Uniemożliwia zestawy rekordów usuwany, ale go nie uniemożliwia ich jest modyfikowany.  Dozwolone modyfikacje obejmują dodawanie i usuwanie rekordów z zestawu rekordów, łącznie z usunięciem wszystkich rekordów, aby pozostawić "empty" zestawu rekordów. Jest to ten sam efekt co usunięcie zestawu z punktu widzenia rozpoznawania DNS rekordów.

Definicje ról niestandardowych obecnie nie można zdefiniować za pomocą portalu Azure. Można utworzyć niestandardową rolę, na podstawie tej definicji roli przy użyciu programu Azure PowerShell:

```powershell
# Create new role definition based on input file
New-AzureRmRoleDefinition -InputFile <file path>
```

Go można również utworzyć za pomocą wiersza polecenia platformy Azure:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

Następnie można przypisać rolę w taki sam sposób jak wbudowane role, zgodnie z opisem we wcześniejszej części tego artykułu.

Aby uzyskać więcej informacji na temat sposobu tworzenia, zarządzania i przypisz role niestandardowe, zobacz [role niestandardowe w Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Blokady zasobów

Oprócz RBAC usługi Azure Resource Manager obsługuje innego rodzaju kontrolę zabezpieczeń, to znaczy możliwość zasobów 'lock'. Gdzie RBAC zasady umożliwiają kontrolowanie akcji konkretnych użytkowników i grup, blokowania zasobów są stosowane do zasobu i obowiązują we wszystkich użytkowników i ról. Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

Istnieją dwa typy zasobów blokady: **DoNotDelete** i **tylko do odczytu**. Mogą być stosowane do strefy DNS lub do poszczególnych zestawu rekordów.  W poniższych sekcjach opisano kilka typowych scenariuszy i sposobu ich obsługi przy użyciu blokady zasobu.

### <a name="protecting-against-all-changes"></a>Ochrona przed wszystkimi zmianami

Aby zapobiec jakichkolwiek zmian, należy zastosować blokady tylko do odczytu do strefy.  Zapobiega to zostanie utworzony i istniejących zestawów rekordów, zmodyfikowane lub usunięte nowych zestawów rekordów.

Blokad zasobów na poziomie strefy można tworzyć za pomocą portalu Azure.  W bloku strefy DNS, kliknij przycisk "Blokady", następnie "Dodaj":

![Blokad zasobów na poziomie strefy za pośrednictwem portalu Azure](./media/dns-protect-zones-recordsets/locks1.png)

Poziomu strefy zasób, którego blokad można również utworzyć za pomocą programu Azure PowerShell:

```powershell
# Lock a DNS zone
New-AzureRmResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Konfigurowanie blokad zasobów platformy Azure nie jest obecnie obsługiwane za pomocą wiersza polecenia platformy Azure.

### <a name="protecting-individual-records"></a>Ochrona poszczególne rekordy

Aby zapobiec istniejącego rekordu DNS ustawiona przed zmianami, dotyczą blokady tylko do odczytu zestawu rekordów.

> [!NOTE]
> Stosowanie blokady DoNotDelete do zestawu rekordów nie jest skutecznej kontroli. Zapobiega zestawu przed usunięciem rekordów, ale go nie uniemożliwia jej jest modyfikowany.  Dozwolone modyfikacje obejmują dodawanie i usuwanie rekordów z zestawu rekordów, łącznie z usunięciem wszystkich rekordów, aby pozostawić "empty" zestawu rekordów. Jest to ten sam efekt co usunięcie zestawu z punktu widzenia rozpoznawania DNS rekordów.

Zestaw rekordów zasobów na poziomie blokad można obecnie tylko skonfigurowany przy użyciu programu Azure PowerShell.  Nie są obsługiwane w portalu Azure lub interfejsu wiersza polecenia Azure.

```powershell
# Lock a DNS record set
New-AzureRmResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Ochrona przed usunięciem strefy

Po usunięciu strefę w usłudze Azure DNS, również zostaną usunięte wszystkie zestawy rekordów w strefie.  Tej operacji nie można cofnąć.  Wpływają firma może mieć przypadkowego usunięcia strefy krytyczne.  W związku z tym jest bardzo ważne, aby chronić przed usunięciem przypadkowemu strefy.

Zastosowanie blokady DoNotDelete do strefy zapobiega strefy przed usunięciem.  Jednak ponieważ blokady są dziedziczone przez zasoby podrzędne, uniemożliwia także żadnych zestawów rekordów w strefie przed usunięciem, który może być niepożądane.  Ponadto zgodnie z opisem w powyższej Uwaga, jest również żadnego efektu, ponieważ nadal można usunąć rekordy z istniejących zestawów rekordów.

Alternatywnie należy rozważyć stosowanie blokady DoNotDelete do rekordu w strefie, takiego jak zestaw rekordów SOA.  Ponieważ nie można usunąć strefy bez także usuwanie zestawów rekordów, chroni to przed usunięciem strefy, umożliwiając zestawów rekordów w strefie, można zmodyfikować za darmo. Jeśli podjęto próbę usunięcia strefy, usługi Azure Resource Manager wykrywa to spowoduje również usunięcie zestawu rekordów SOA i blokuje połączenia, ponieważ SOA jest zablokowany.  Brak zestawów rekordów są usuwane.

Następujące polecenie programu PowerShell tworzy DoNotDelete blokadą rekord SOA strefy danego:

```powershell
# Protect against zone delete with DoNotDelete lock on the record set
New-AzureRmResourceLock -LockLevel DoNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Innym sposobem uniknięcia strefy przypadkowego usunięcia jest przy użyciu niestandardowej roli zabezpieczeń zapewniające operatora i konta usług używane do zarządzania stref ma strefy usunąć uprawnienia. Gdy chcesz usunąć strefę, można wymusić delete dwuetapową, pierwszy udzielającym strefy uprawnienia do usuwania (w zakresie strefy, aby uniemożliwić usunięcie niewłaściwy strefy) i drugi usunąć strefę.

Takie podejście drugi ma tę zaletę, jego działanie dla wszystkich stref, które uzyskują do tych kont bez konieczności Pamiętaj, aby utworzyć wszystkie blokady. Ma ona wadą czy kont z uprawnienia do usuwania strefy, takich jak właściciel subskrypcji może nadal przypadkowo usunąć strefę krytyczne.

Istnieje możliwość użycia obu podejść — blokowania zasobów i role niestandardowe — w tym samym czasie jako podejściu obrony zabezpieczeń do ochrony strefy DNS.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat pracy z RBAC, zobacz [wprowadzenie do zarządzania dostępem w portalu Azure](../role-based-access-control/overview.md).
* Aby uzyskać więcej informacji na temat pracy z blokowania zasobów, zobacz [blokowania zasobów z usługi Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

