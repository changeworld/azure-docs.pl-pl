---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597948"
---
## <a name="attack-scenario"></a>Scenariusz ataku

Ataki siłowe często cel portów zarządzania jako sposób, aby uzyskać dostęp do maszyny Wirtualnej. Jeśli się powiedzie, osoba atakująca może przejąć kontrolę nad maszyną wirtualną i ustanowić przyczółek w twoim środowisku.

Jednym ze sposobów na zmniejszenie narażenia na atak siłą brutalną jest ograniczenie czasu otwarcia portu. Porty zarządzania nie muszą być zawsze otwarte. Muszą być otwarte tylko wtedy, gdy masz połączenie z maszyną wirtualną, na przykład do wykonywania zadań zarządzania lub konserwacji. Gdy opcja just-in-time jest włączona, usługa Security Center używa [reguł sieciowej grupy zabezpieczeń](../articles/virtual-network/security-overview.md#security-rules) (NSG) i zapory platformy Azure, które ograniczają dostęp do portów zarządzania, dzięki czemu osoby atakujące nie mogą być kierowane przez osoby atakujące.

![Scenariusz just-in-time](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Jak działa dostęp do JIT?

Gdy opcja just-in-time jest włączona, Usługa Security Center blokuje ruch przychodzący do maszyn wirtualnych platformy Azure, tworząc regułę sieciowej sieciowej sieciowej. Wybierz porty na maszynie Wirtualnej, do których ruch przychodzący zostanie zablokowany. Porty te są kontrolowane przez rozwiązanie just-in-time.

Gdy użytkownik żąda dostępu do maszyny Wirtualnej, Usługa Security Center sprawdza, czy użytkownik ma uprawnienia kontroli dostępu opartej na [rolach (RBAC)](../articles/role-based-access-control/role-assignments-portal.md) dla tej maszyny Wirtualnej. Jeśli żądanie zostanie zatwierdzone, Usługa Security Center automatycznie konfiguruje sieciowe grupy zabezpieczeń (NSG) i zaporę platformy Azure, aby zezwolić na ruch przychodzący do wybranych portów i żądanych źródłowych adresów IP lub zakresów, przez określony czas. Po upływie tego czasu usługa Security Center przywraca cele zabezpieczeń do poprzednich stanów. Te połączenia, które zostały już ustanowione, nie są jednak przerywane.

 > [!NOTE]
 > Jeśli żądanie dostępu JIT zostanie zatwierdzone dla maszyny Wirtualnej za zaporą platformy Azure, centrum zabezpieczeń automatycznie zmienia reguły zasad sieciowej grupy zabezpieczeń i zapory. Przez określony czas reguły zezwalają na ruch przychodzący do wybranych portów i żądanych źródłowych adresów IP lub zakresów. Po upływie tego czasu usługa Security Center przywraca reguły zapory i sieciowej sieciowej sieciowej do poprzednich stanów.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Uprawnienia potrzebne do konfigurowania i używania JIT

| Aby umożliwić użytkownikowi: | Uprawnienia do ustawiania|
| --- | --- |
| Konfigurowanie lub edytowanie zasad JIT dla maszyny Wirtualnej | *Przypisz te akcje do roli:*  <ul><li>W zakresie subskrypcji lub grupy zasobów, która jest skojarzona z maszyną wirtualną:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> W zakresie subskrypcji lub grupy zasobów maszyny Wirtualnej: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Żądanie dostępu JIT do maszyny Wirtualnej | *Przypisz następujące akcje do użytkownika:*  <ul><li>W zakresie subskrypcji lub grupy zasobów, która jest skojarzona z maszyną wirtualną:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>W zakresie subskrypcji lub grupy zasobów, która jest skojarzona z maszyną wirtualną:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  W zakresie subskrypcji lub grupy zasobów lub maszyny Wirtualnej:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  W zakresie subskrypcji lub grupy zasobów lub maszyny Wirtualnej:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|