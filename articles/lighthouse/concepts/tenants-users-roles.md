---
title: Dzierżawcy, role i użytkownicy w scenariuszach latarni morskiej platformy Azure
description: Poznaj pojęcia dzierżaw, użytkowników i ról usługi Azure Active Directory, a także sposób ich użycia w scenariuszach usługi Azure Lighthouse.
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 7540e17fd80f9a1d8e996295000c126614b838d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246895"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Dzierżawcy, role i użytkownicy w scenariuszach latarni morskiej platformy Azure

Przed dołączaniem klientów do [zarządzania zasobami delegowanymi platformy Azure](azure-delegated-resource-management.md)należy zrozumieć, jak działają dzierżawcy, użytkownicy i role usługi Azure Active Directory (Azure AD), a także jak mogą być używane w scenariuszach usługi Azure Lighthouse.

*Dzierżawa* jest dedykowanym i zaufanym wystąpieniem usługi Azure AD. Zazwyczaj każda dzierżawa reprezentuje jedną organizację. Zarządzanie zasobami delegowanymi platformy Azure umożliwia logiczne projekcji zasobów z jednej dzierżawy do innej dzierżawy. Dzięki temu użytkownicy w dzierżawie zarządzającej (na przykład należącej do dostawcy usług) mogą uzyskiwać dostęp do delegowanych zasobów w dzierżawie klienta lub umożliwia [przedsiębiorstwom z wieloma dzierżawcami scentralizowanie operacji zarządzania.](enterprise.md)

Aby osiągnąć tę projekcję logiczną, subskrypcja (lub co najmniej jedna grupa zasobów w ramach subskrypcji) w dzierżawie klienta musi być *dołączana* do zarządzania zasobami delegowanymi platformy Azure. Ten proces dołączania można wykonać [za pomocą szablonów usługi Azure Resource Manager](../how-to/onboard-customer.md) lub publikując ofertę [publiczną lub prywatną w portalu Azure Marketplace.](../how-to/publish-managed-services-offers.md)

Niezależnie od wybranej metody dołączania, należy zdefiniować *autoryzacje*. Każda autoryzacja określa konto użytkownika w dzierżawie zarządzającej, które będzie miało dostęp do delegowanych zasobów, oraz wbudowaną rolę, która ustawia uprawnienia, które każdy z tych użytkowników będzie miał dla tych zasobów.

## <a name="role-support-for-azure-delegated-resource-management"></a>Obsługa ról dla zarządzania zasobami delegowanymi platformy Azure

Podczas definiowania autoryzacji każdemu kontu użytkownika musi być przypisana jedna z [wbudowanych ról kontroli dostępu opartej na rolach (RBAC).](../../role-based-access-control/built-in-roles.md) Role niestandardowe i [klasyczne role administratora subskrypcji](../../role-based-access-control/classic-administrators.md) nie są obsługiwane.

Wszystkie [wbudowane role](../../role-based-access-control/built-in-roles.md) są obecnie obsługiwane przez zarządzanie zasobami delegowanymi platformy Azure, z następującymi wyjątkami:

- [Rola Właściciel](../../role-based-access-control/built-in-roles.md#owner) nie jest obsługiwana.
- Wszystkie wbudowane role z [uprawnieniami DataActions](../../role-based-access-control/role-definitions.md#dataactions) nie są obsługiwane.
- Wbudowana rola [Administratora dostępu użytkownika](../../role-based-access-control/built-in-roles.md#user-access-administrator) jest obsługiwana, ale tylko w ograniczonym celu [przypisywania ról do tożsamości zarządzanej w dzierżawie klienta.](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant) Żadne inne uprawnienia zazwyczaj przyznane przez tę rolę będą miały zastosowanie. W przypadku definiowania użytkownika z tą rolą należy również określić wbudowane role, które ten użytkownik może przypisać do tożsamości zarządzanych.

> [!NOTE]
> Po dodaniu odpowiedniej nowej wbudowanej roli do platformy Azure można ją przypisać podczas [dołączania do klienta przy użyciu szablonów usługi Azure Resource Manager.](../how-to/onboard-customer.md) Może wystąpić opóźnienie, zanim nowo dodana rola stanie się dostępna w portalu cloud partner podczas [publikowania oferty usługi zarządzanej.](../how-to/publish-managed-services-offers.md)

## <a name="best-practices-for-defining-users-and-roles"></a>Najważniejsze wskazówki dotyczące definiowania użytkowników i ról

Podczas tworzenia autoryzacji zalecamy następujące najlepsze rozwiązania:

- W większości przypadków należy przypisać uprawnienia do grupy użytkowników usługi Azure AD lub jednostki usługi, a nie do serii kont indywidualnych użytkowników. Dzięki temu można dodać lub usunąć dostęp dla poszczególnych użytkowników bez konieczności aktualizowania i ponownego publikowania planu po zmianie wymagań dostępu.
- Pamiętaj, aby przestrzegać zasady najmniejszych uprawnień, aby użytkownicy mieli tylko uprawnienia potrzebne do ukończenia zadania, co pomaga zmniejszyć ryzyko niezamierzonych błędów. Aby uzyskać więcej informacji, zobacz [Zalecane praktyki zabezpieczeń](../concepts/recommended-security-practices.md).
- Dołącz użytkownika z [rolą usuwania przydziału rejestracji usług zarządzanych,](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) aby w razie potrzeby [usunąć dostęp do delegacji.](../how-to/onboard-customer.md#remove-access-to-a-delegation) Jeśli ta rola nie jest przypisana, delegowane zasoby mogą zostać usunięte tylko przez użytkownika w dzierżawie klienta.
- Upewnij się, że każdy użytkownik, który musi [wyświetlić stronę Moi klienci w witrynie Azure portal](../how-to/view-manage-customers.md) ma rolę [czytnika](../../role-based-access-control/built-in-roles.md#reader) (lub inną wbudowaną rolę, która obejmuje dostęp do czytnika).

> [!IMPORTANT]
> Aby dodać uprawnienia dla grupy usługi Azure AD, **typem grupy** musi być **security,** a nie **Office 365**. Ta opcja jest zaznaczona podczas tworzenia grupy. Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowej grupy i dodawanie członków w usłudze Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zalecanych praktykach w zakresie zabezpieczeń dotyczących zarządzania zasobami delegowanymi platformy Azure](recommended-security-practices.md).
- Dołączanie klientów do zarządzania zasobami delegowanymi platformy Azure za [pomocą szablonów usługi Azure Resource Manager](../how-to/onboard-customer.md) lub przez [opublikowanie prywatnej lub publicznej oferty usług zarządzanych w portalu Azure Marketplace.](../how-to/publish-managed-services-offers.md)
