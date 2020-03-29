---
title: Konfigurowanie środowiska dla operatora planu
description: Dowiedz się, jak skonfigurować środowisko platformy Azure do użytku z wbudowaną rolą kontroli dostępu opartej na rolach (RBAC) operatora planu.
ms.date: 08/26/2019
ms.topic: how-to
ms.openlocfilehash: fba0dd3f2eeb69f768800d1d04640510462d3c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873220"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>Konfigurowanie środowiska dla operatora strategii

Zarządzanie definicjami planu i przydziałami planu można przypisać do różnych zespołów. Często jest architekt lub zespół nadzoru, aby być odpowiedzialny za zarządzanie cyklem życia definicji planu, podczas gdy zespół operacyjny jest odpowiedzialny za zarządzanie przydziałami tych centralnie kontrolowanych definicji planu.

**Wbudowana** kontrola dostępu oparta na rolach Blueprint operator jest zaprojektowana specjalnie do użycia w scenariuszu tego typu. Rola umożliwia zespołom typu operacyjnego zarządzanie przypisaniem definicji planu organizacji, ale nie możliwość ich modyfikowania. W ten sposób wymaga pewnej konfiguracji w środowisku platformy Azure i w tym artykule wyjaśniono niezbędne kroki.

## <a name="grant-permission-to-the-blueprint-operator"></a>Udzielanie uprawnień operatorowi planu

Pierwszym krokiem jest przyznanie roli **operatora planu** do konta lub grupy zabezpieczeń (zalecane), która będzie przypisywanie planów. Ta akcja powinna być wykonana na najwyższym poziomie w hierarchii grupy zarządzania, która obejmuje wszystkie grupy zarządzania i subskrypcje, do których zespół operacyjny powinien mieć dostęp do przydziału planu. Zaleca się przestrzeganie zasady najmniejszych uprawnień podczas udzielania tych uprawnień.

1. (Zalecane) [Tworzenie grupy zabezpieczeń i dodawanie członków](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. [Dodawanie przypisania roli](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) **operatora planu** do konta lub grupy zabezpieczeń

## <a name="user-assign-managed-identity"></a>Tożsamość zarządzana przypisywana przez użytkownika

Definicja planu może używać tożsamości zarządzanych przypisanych do systemu lub przypisanych przez użytkownika. Jednak podczas korzystania z roli **operatora planu,** definicja planu musi być skonfigurowana do używania tożsamości zarządzanej przypisanej przez użytkownika. Ponadto konto lub grupa zabezpieczeń, którym przyznano rolę **Operator planu,** musi zostać przyznana rola **operatora tożsamości zarządzanej** w tożsamości zarządzanej przypisanej przez użytkownika. Bez tego uprawnienia przypisania planu nie powiodą się z powodu braku uprawnień.

1. [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) do użycia przez przypisany plan

1. [Dodaj przypisanie roli](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) **operatora tożsamości zarządzanej** do konta lub grupy zabezpieczeń. Zakres przypisania roli do nowej tożsamości zarządzanej przypisanej przez użytkownika.

1. Jako **operator planu** [przypisz plan,](../create-blueprint-portal.md#assign-a-blueprint) który używa nowej tożsamości zarządzanej przypisanej przez użytkownika.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [cyklu życia planu](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).