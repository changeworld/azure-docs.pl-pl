---
title: Zarządzanie zasobami delegowanymi na platformie Azure
description: Oferty usług zarządzanych umożliwiają dostawcom usług sprzedawanie ofert zarządzania zasobami klientom w portalu Azure Marketplace.
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: db9f562ca4f42d1c1d85eeac44495a8ec7e01beb
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548420"
---
# <a name="azure-delegated-resource-management"></a>Zarządzanie zasobami delegowanymi na platformie Azure

Zarządzanie zasobami delegowanymi platformy Azure jest jednym z kluczowych składników latarni morskiej platformy Azure. Dzięki zarządzania zasobami delegowanymi platformy Azure dostawcy usług mogą uprościć zaangażowanie klientów i środowisko dołączania, jednocześnie zarządzając delegowanymi zasobami na dużą skalę z elastycznością i precyzją.

## <a name="what-is-azure-delegated-resource-management"></a>Co to jest zarządzanie zasobami delegowanymi platformy Azure?

Zarządzanie zasobami delegowanymi platformy Azure umożliwia logiczne projekcji zasobów z jednej dzierżawy do innej dzierżawy. Dzięki temu autoryzowani użytkownicy w jednej dzierżawie usługi Azure Active Directory (Azure AD) mogą wykonywać operacje zarządzania w różnych dzierżawach usługi Azure AD należących do ich klientów. Dostawcy usług mogą logować się do własnej dzierżawy usługi Azure AD i mieć uprawnienia do pracy w delegowanych subskrypcji klientów i grup zasobów. Dzięki temu mogą wykonywać operacje zarządzania w imieniu swoich klientów, bez konieczności logowania się do poszczególnych dzierżawców klienta.

> [!NOTE]
> Zarządzanie zasobami delegowanymi platformy Azure może być również używane [w przedsiębiorstwie, które ma wielu dzierżaw usługi Azure AD,](enterprise.md) aby uprościć zarządzanie między dzierżawcami.

Dzięki zarządzania zasobami delegowanymi platformy Azure autoryzowani użytkownicy mogą pracować bezpośrednio w kontekście subskrypcji klienta bez posiadania konta w dzierżawie tego klienta lub bycia współwłaścicielem dzierżawy klienta. Mogą również [wyświetlać i zarządzać wszystkimi delegowanymi subskrypcjami klientów na nowej stronie **Moi klienci** ](../how-to/view-manage-customers.md) w witrynie Azure portal.

[Środowisko zarządzania między dzierżawcami](cross-tenant-management-experience.md) ułatwia wydajniejszą pracę z usługami zarządzania platformy Azure, takimi jak Azure Policy, Azure Security Center i inne. Wszystkie działania dostawcy usług są śledzone w dzienniku działań, który jest przechowywany w dzierżawie klienta (i mogą być wyświetlane przez użytkowników w dzierżawie zarządzającej). Oznacza to, że zarówno klient, jak i dostawca usług mogą łatwo zidentyfikować użytkownika skojarzonego z wszelkimi zmianami.

Po pokładzie klienta do usługi Azure zarządzania zasobami delegowanymi, będą mieli dostęp do nowej strony **dostawców usług** w witrynie Azure portal, gdzie mogą [potwierdzić i zarządzać swoimi ofertami, dostawcami usług i delegowanymi zasobami](../how-to/view-manage-service-providers.md). Jeśli klient kiedykolwiek chce odwołać dostęp dla usługodawcy, może to zrobić tutaj w dowolnym momencie.

Nowy [typ oferty usługi zarządzanej można opublikować w portalu Azure Marketplace,](../how-to/publish-managed-services-offers.md) aby łatwo dołączać klientów do zarządzania zasobami delegowanymi platformy Azure. Alternatywnie można [ukończyć proces dołączania, wdrażając szablony usługi Azure Resource Manager](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Jak działa zarządzanie delegowanymi zasobami platformy Azure

Na wysokim poziomie, oto jak działa zarządzanie zasobami delegowanymi platformy Azure:

1. Jako dostawca usług można zidentyfikować dostęp (role), które grupy, podmioty usługi lub użytkownicy będą musieli zarządzać zasobami platformy Azure klienta. Definicja dostępu zawiera identyfikator dzierżawy dostawcy usług wraz z wymaganym dostępem dla oferty, zdefiniowanym przy użyciu tożsamości **principalId** z dzierżawy mapowanych do [wbudowanych wartości **funkcjiDefinacja** ](../../role-based-access-control/built-in-roles.md) (współautor, współautor maszyn wirtualnych, czytnik itp.).
2. Określisz ten dostęp i dołączanie klienta do zarządzania zasobami delegowanymi platformy Azure na jeden z dwóch sposobów:
   - [Publikowanie oferty usług zarządzanych w portalu Azure Marketplace](../how-to/publish-managed-services-offers.md) (prywatnej lub publicznej), którą klient zaakceptuje
   - [Wdrażanie szablonu usługi Azure Resource Manager w dzierżawie klienta](../how-to/onboard-customer.md) dla jednej lub większej liczby określonych subskrypcji lub grup zasobów
3. Po uruchomieniu klienta autoryzowani użytkownicy mogą logować się do dzierżawy dostawcy usług i wykonywać zadania zarządzania w danym zakresie klienta, na podstawie zdefiniowanego dostępu.

> [!NOTE]
> Delegowanie subskrypcji między dwoma dzierżawami w oddzielnych chmurach nie jest obsługiwane.

## <a name="support-for-azure-delegated-resource-management"></a>Obsługa zarządzania zasobami delegowanymi platformy Azure

Jeśli potrzebujesz pomocy związanej z zarządzaniem zasobami delegowanymi platformy Azure, możesz otworzyć żądanie pomocy technicznej w witrynie Azure portal. W przypadku **typu problem**wybierz pozycję **Techniczne**. Wybierz subskrypcję, a następnie wybierz **latarnię morską** (w obszarze **Monitorowanie & Zarządzanie).**

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [środowiskach zarządzania między dzierżawcami](cross-tenant-management-experience.md).
- Dowiedz się więcej o [ofertach usług zarządzanych w portalu Azure Marketplace.](managed-services-offers.md)