---
title: Zarządzanie zasobami delegowanymi na platformie Azure
description: Dzięki usługom zarządzanym dostawcy usług mogą sprzedawać oferty zarządzania zasobami klientom w portalu Azure Marketplace.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 8cc75fde348d070a19d1987d1403c63e8e6d2278
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286639"
---
# <a name="azure-delegated-resource-management"></a>Zarządzanie zasobami delegowanymi na platformie Azure

Zarządzanie zasobami delegowanymi przez platformę Azure to jeden z najważniejszych składników usługi Azure Lighthouse. Dzięki funkcji zarządzania zasobami delegowanymi na platformie Azure dostawcy usług mogą uprościć zaangażowanie klientów i dołączanie, a jednocześnie zarządzać zasobami delegowanymi na dużą skalę z elastycznością i dokładnością.

## <a name="what-is-azure-delegated-resource-management"></a>Co to jest zarządzanie zasobami delegowanymi przez platformę Azure?

Zarządzanie zasobami delegowanymi przez platformę Azure umożliwia logiczne rzutowanie zasobów z jednej dzierżawy na inną dzierżawcę. Umożliwia to autoryzowanym użytkownikom w jednej Azure Active Directory (Azure AD) wykonywanie operacji zarządzania w różnych dzierżawach usługi Azure AD należących do klientów. Dostawcy usług mogą zalogować się do swojej dzierżawy usługi Azure AD i mieć autoryzację do pracy w delegowanych subskrypcjach klientów i grupach zasobów. Pozwala to na wykonywanie operacji zarządzania w imieniu swoich klientów, bez konieczności logowania się do poszczególnych dzierżaw klientów.

> [!NOTE]
> Zarządzanie zasobami delegowanymi przez platformę Azure może być również używane w przedsiębiorstwie, które ma wiele dzierżawców usługi Azure AD, aby uprościć zarządzanie między dzierżawcami.

Dzięki funkcji zarządzania zasobami delegowanymi na platformie Azure autoryzowani użytkownicy mogą współpracować bezpośrednio w kontekście subskrypcji klienta bez konieczności używania konta w dzierżawie klienta lub współwłaścicielem dzierżawy klienta. Mogą także [wyświetlać wszystkie delegowane subskrypcje klientów i zarządzać nimi na stronie nowe **moi klienci** ](../how-to/view-manage-customers.md) w Azure Portal.

[Środowisko zarządzania przez wiele dzierżawców](cross-tenant-management-experience.md) pomaga wydajniej współpracować z usługami zarządzania platformy Azure, takimi jak Azure Policy, Azure Security Center i nie tylko. Wszystkie działania dostawcy usług są śledzone w dzienniku aktywności, który jest przechowywany w dzierżawach dostawcy usług i klienta. Oznacza to, że zarówno klient, jak i dostawca usług mogą łatwo identyfikować użytkownika skojarzonego ze wszystkimi zmianami.

Po dołączeniu klienta do zarządzania zasobami delegowanymi przez platformę Azure użytkownicy będą mieli dostęp do nowej strony **dostawcy usług** w Azure Portal, w której będą mogli [potwierdzić swoje oferty, dostawców usług i zasoby delegowane oraz zarządzać nimi](../how-to/view-manage-service-providers.md). Jeśli klient kiedykolwiek chce odwołać dostęp dla dostawcy usług, może to zrobić w dowolnym momencie.

[Nowy typ oferty usługi zarządzanej można opublikować w witrynie Azure Marketplace](../how-to/publish-managed-services-offers.md) , aby w łatwy sposób dołączać klientów do zarządzania zasobami delegowanymi przez platformę Azure. Możesz również [ukończyć proces dołączania, wdrażając szablony Azure Resource Manager](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Jak działa zarządzanie zasobami delegowanymi przez platformę Azure

Na wysokim poziomie poniżej przedstawiono sposób działania zarządzania zasobami delegowanymi przez platformę Azure:

1. Jako dostawca usług identyfikujesz dostęp (role), których grupy, nazwy główne usług lub użytkownicy będą musieli zarządzać zasobami platformy Azure klienta. Definicja dostępu zawiera identyfikator dzierżawy dostawcy usług wraz z wymaganym dostępem do oferty, zdefiniowanym przy użyciu tożsamości **principalId** z dzierżawy zamapowanej do [wbudowanych wartości **definicji** ](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (współautor, maszyna wirtualna Współautor, czytelnik itp.).
2. Należy określić ten dostęp i dołączyć klienta do zarządzania zasobami delegowanymi przez platformę Azure na jeden z dwóch sposobów:
   - [Publikowanie oferty usług zarządzanych w portalu Azure Marketplace](../how-to/publish-managed-services-offers.md) (prywatnej lub publicznej), która zostanie zaakceptowana przez klienta
   - [Wdrażanie szablonu Azure Resource Manager w dzierżawie klienta](../how-to/onboard-customer.md) dla jednej lub wielu określonych subskrypcji lub grup zasobów
3. Po dołączeniu klienta autoryzowani użytkownicy mogą zalogować się do dzierżawy dostawcy usługi i wykonywać zadania zarządzania w danym zakresie klienta na podstawie zdefiniowanego dostępu.

## <a name="support-for-azure-delegated-resource-management"></a>Obsługa zarządzania zasobami delegowanymi przez platformę Azure

Jeśli potrzebujesz pomocy związanej z zarządzaniem zasobami delegowanymi przez platformę Azure, możesz otworzyć żądanie pomocy technicznej w Azure Portal. W obszarze **typ problemu**wybierz pozycję **techniczne**. Wybierz subskrypcję, a następnie wybierz pozycję **delegowane zarządzanie zasobami** (w obszarze **monitorowanie & Zarządzanie**).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](cross-tenant-management-experience.md).
- Dowiedz się więcej o [ofertach usług zarządzanych w witrynie Azure Marketplace](managed-services-offers.md).