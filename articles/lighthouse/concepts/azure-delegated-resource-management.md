---
title: Azure delegowane zarządzanie zasobami - morskiej platformy Azure
description: Zarządzane usługi, które oferuje umożliwiają dostawcom usług sprzedaży zasobów zarządzania ofert dla klientów w witrynie Azure Marketplace.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: cec6453cdf339e82420a1b12af6c8e60526fdc03
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809967"
---
# <a name="azure-delegated-resource-management"></a>Zarządzanie zasobami platformy Azure delegowanego

Zarządzanie zasobami platformy Azure delegowanego jest jednym z najważniejsze składniki morskiej platformy Azure. Za pomocą funkcji zarządzania usługi Azure resource delegowanego usługodawca może uprościć zaangażowania i dołączania środowisk współpracy z klientami, oraz zarządzanie delegowane zasobów na dużą skalę przy zachowaniu elastyczności i dokładność.

## <a name="what-is-azure-delegated-resource-management"></a>Co to jest Azure delegowane zarządzanie zasobami?

Zarządzanie zasobami platformy Azure delegowanego umożliwia logiczne projekcji zasobów z jednej dzierżawy do innej dzierżawy. Dzięki temu autoryzowanych użytkowników jednej dzierżawy usługi Azure Active Directory (Azure AD) i wykonywać operacje zarządzania w usłudze Azure AD w różnych dzierżaw należące do klientów. Dostawcy usług mogą zalogować się do ich własnej dzierżawy usługi Azure AD i ma autoryzacji do pracy w subskrypcji klienta delegowanego i grup zasobów. W ten sposób je wykonywać operacje zarządzania w imieniu swoich klientów bez konieczności logowania się do poszczególnych dzierżaw poszczególnych klientów.

> [!NOTE]
> Zarządzanie zasobami platformy Azure delegowanego można również w przedsiębiorstwie, mającej wielu dzierżaw usługi Azure AD swoje własne w celu uproszczenia zarządzania wielu dzierżawach.

Za pomocą funkcji zarządzania usługi Azure resource delegowanego autoryzowani użytkownicy mogą pracować bezpośrednio w ramach subskrypcji klienta bez możliwości konta w dzierżawie klienta lub współwłaściciel dzierżawy klienta. Mogą również [Wyświetl i Zarządzaj wszystkimi subskrypcjami klientów delegowanego w nowym **moich klientów** strony](../how-to/view-manage-customers.md) w witrynie Azure portal.

[Międzydzierżawowa środowisko zarządzania](cross-tenant-management-experience.md) pomaga pracować wydajniej przy użyciu usługi zarządzania platformą Azure, takich jak usługi Azure Policy, Azure Security Center i inne. Wszystkie działania dostawcy usługi jest śledzona w dzienniku aktywności, który jest przechowywany w dzierżawach klienta i dostawcy usług. Oznacza to, Dostawca klienta i usługi można łatwo zidentyfikować użytkownika, skojarzone ze wszystkimi zmianami.

Po dołączeniu klienta na platformie Azure delegowane zarządzanie zasobami będą mieć dostęp do nowego **dostawców usług** strony w witrynie Azure portal, która umożliwia im [upewnij się i zarządzanie nimi ich oferty, dostawców usług i delegowane zasobów](../how-to/view-manage-service-providers.md). Jeśli klient chce nigdy nie mogła odwołać dostępu dla dostawcy usług, mogą to zrobić tutaj w dowolnym momencie.

Możesz [publikować nowy typ oferty usług zarządzanych w portalu Azure Marketplace](../how-to/publish-managed-services-offers.md) do łatwego angażowania nowych klientów na platformie Azure delegowane zarządzanie zasobami. Możesz też [ukończenia procesu dołączania, wdrażając szablonów usługi Azure Resource Manager](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Działa jak Azure delegowane zarządzanie zasobami

Na wysokim poziomie poniżej przedstawiono, jak Azure delegowane przebieg zarządzania zasobów:

1. Jako dostawca usług należy zidentyfikować dostępu (role), który z grup, jednostek usługi lub użytkownicy będą musieli zarządzać zasobami platformy Azure przez klienta. Definicja dostępu zawiera identyfikator dzierżawy dostawcy usług oraz wymagany dostęp do tej oferty, definiowane przy użyciu **principalId** tożsamości z dzierżawą mapowane na [wbudowanych  **definicji roleDefinition** wartości](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (Współautor Współautor maszyny Wirtualnej, Czytelnik, itp.).
2. Określ dostęp i dołączanie klientów na platformie Azure delegowane zarządzanie zasobami w jeden z dwóch sposobów:
   - [Publikowanie oferty usług zarządzanych platformy Azure Marketplace](../how-to/publish-managed-services-offers.md) (prywatnej lub publicznej), klient będzie akceptować
   - [Wdrażanie szablonu usługi Azure Resource Manager do dzierżawy klienta](../how-to/onboard-customer.md) dla określonej subskrypcji lub grupy zasobów
3. Po klienta zostało dołączone, autoryzowanych użytkowników można zalogować się do dzierżawy usługi dostawcy i wykonywać zadania zarządzania w zakresie danego klienta, na podstawie uprawnień, który zdefiniowano.

## <a name="support-for-azure-delegated-resource-management"></a>Pomoc dotycząca platformy Azure delegowane zarządzanie zasobami

Jeśli potrzebujesz pomocy, związane z platformy Azure delegowane zarządzanie zasobami, możesz otworzyć żądania pomocy technicznej w witrynie Azure portal. Aby uzyskać **typ problemu**, wybierz **techniczne**. Wybierz subskrypcję, a następnie wybierz **delegowane zarządzanie zasobami** (w obszarze **monitorowanie i zarządzanie**).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [międzydzierżawowa środowiska zarządzania](cross-tenant-management-experience.md).
- Dowiedz się więcej o [zarządzane oferty usług w witrynie Azure Marketplace](managed-services-offers.md).