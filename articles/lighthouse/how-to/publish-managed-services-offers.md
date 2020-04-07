---
title: Publikowanie oferty usługi zarządzanej w portalu Azure Marketplace
description: Dowiedz się, jak opublikować ofertę usługi zarządzanej, która dołącza klientów do zarządzania zasobami delegowanymi platformy Azure.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 922e78a0715ce5c758ca7d068a38af43e214a524
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673931"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publikowanie oferty usługi zarządzanej w portalu Azure Marketplace

W tym artykule dowiesz się, jak opublikować publiczną lub prywatną ofertę usług zarządzanych w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) przy użyciu programu Portalu Marketplace [komercyjnego](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) w [Centrum partnerskim.](https://partner.microsoft.com/) Klienci, którzy zakupią ofertę, mogą następnie dołączać subskrypcje i grupy zasobów do [zarządzania zasobami delegowanymi platformy Azure.](../concepts/azure-delegated-resource-management.md)

## <a name="publishing-requirements"></a>Wymagania dotyczące publikowania

Aby tworzyć i publikować oferty, musisz mieć [prawidłowe konto w Centrum partnerskim.](../../marketplace/partner-center-portal/create-account.md) Jeśli nie masz jeszcze konta, [proces rejestracji](https://aka.ms/joinmarketplace) doprowadzi Cię przez etapy tworzenia konta w Centrum partnerów i rejestrowania się w programie Marketplace komercyjnych.

Zgodnie z [wymaganiami certyfikacji oferty managed service](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)musisz mieć [poziom kompetencji Silver lub Gold Cloud Platform](https://docs.microsoft.com/partner-center/learn-about-competencies) lub być [usługą Azure Expert MSP,](https://partner.microsoft.com/membership/azure-expert-msp) aby opublikować ofertę usługi zarządzanej.

Identyfikator sieci Partner Network (MPN) firmy Microsoft zostanie [automatycznie skojarzony](../../billing/billing-partner-admin-link-started.md) z publikowanych ofertami w celu śledzenia wpływu na zaangażowanie klientów.

> [!NOTE]
> Jeśli nie chcesz publikować oferty w portalu Azure Marketplace, możesz ręcznie dołączać klientów przy użyciu szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Dołączanie klienta do zarządzania zasobami delegowanymi platformy Azure](onboard-customer.md).

## <a name="create-your-offer"></a>Stwórz swoją ofertę

Aby uzyskać szczegółowe instrukcje dotyczące tworzenia oferty, w tym wszystkie informacje i zasoby, które należy podać, zobacz [Tworzenie nowej oferty usług zarządzanych](../../marketplace/partner-center-portal/create-new-managed-service-offer.md).

Aby dowiedzieć się więcej o ogólnym procesie publikowania, zobacz [Przewodnik publikowania w portalu Azure Marketplace i AppSource](../../marketplace/marketplace-publishers-guide.md). Należy również zapoznać się z [zasadami certyfikacji rynku komercyjnego,](https://docs.microsoft.com/legal/marketplace/certification-policies)w szczególności sekcją [Usługi zarządzane.](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)

Gdy klient doda ofertę, będzie mógł delegować jedną lub więcej subskrypcji lub grup zasobów, które następnie będą [dołączane do zarządzania zasobami delegowanymi platformy Azure.](#the-customer-onboarding-process)

> [!IMPORTANT]
> Każdy plan w ofercie usługi zarządzanej zawiera sekcję **Szczegóły manifestu,** w której definiujesz jednostki usługi Azure Active Directory (Azure AD) w dzierżawie, które będą miały dostęp do delegowanych grup zasobów i/lub subskrypcji dla klientów, którzy zakupią ten plan. Ważne jest, aby mieć świadomość, że każda grupa (lub użytkownik lub usługodawca), który zostanie uwzględniony, będzie miała takie same uprawnienia dla każdego klienta, który kupuje plan. Aby przypisać różne grupy do pracy z każdym klientem, musisz opublikować oddzielny [plan prywatny,](../../marketplace/private-offers.md) który jest dostępny wyłącznie dla każdego klienta.

## <a name="publish-your-offer"></a>Opublikuj swoją ofertę

Po zakończeniu wszystkich sekcji następnym krokiem jest opublikowanie oferty w portalu Azure Marketplace. Wybierz przycisk **Publikuj,** aby zainicjować proces tworzenia oferty na żywo. Więcej informacji na temat tego procesu można znaleźć [tutaj](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish).

W każdej chwili możesz [opublikować zaktualizowaną wersję swojej oferty.](../../marketplace/partner-center-portal/update-existing-offer.md) Na przykład można dodać nową definicję roli do wcześniej opublikowanej oferty. Gdy to zrobisz, klienci, którzy już dodali ofertę zobaczą ikonę na stronie [**Dostawcy usług**](view-manage-service-providers.md) w witrynie Azure portal, która informuje ich, że dostępna jest aktualizacja. Każdy klient będzie mógł [przejrzeć zmiany](view-manage-service-providers.md#update-service-provider-offers) i zdecydować, czy chce zaktualizować do nowej wersji. 

## <a name="the-customer-onboarding-process"></a>Proces dołączania klienta

Po dodaniem oferty przez klienta będzie można [delegować jedną lub więcej określonych subskrypcji lub grup zasobów,](view-manage-service-providers.md#delegate-resources)które następnie będą dołączane do zarządzania zasobami delegowanymi platformy Azure. Jeśli klient zaakceptował ofertę, ale nie przekazał jeszcze żadnych zasobów, zobaczy notatkę w górnej części dostawcy **oferuje** sekcji [**dostawcy usługi**](view-manage-service-providers.md) strony strony w witrynie Azure portal.

> [!IMPORTANT]
> Delegowanie musi być wykonywane przez konto niebędące gościem w dzierżawie klienta, który ma [wbudowaną rolę Właściciela](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) dla subskrypcji jest dołączany (lub który zawiera grupy zasobów, które są dołączane). Aby wyświetlić wszystkich użytkowników, którzy mogą delegować subskrypcję, użytkownik w dzierżawie klienta może wybrać subskrypcję w portalu Azure, otworzyć **formant dostępu (IAM)** i [wyświetlić wszystkich użytkowników z rolą Właściciel](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Gdy klient deleguje subskrypcję (lub jedną lub więcej grup zasobów w ramach subskrypcji), dostawca zasobów **Microsoft.ManagedServices** zostanie zarejestrowany dla tej subskrypcji, a użytkownicy w dzierżawie będą mogli uzyskać dostęp do zasobów delegowanych zgodnie z uprawnieniami w ofercie.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [rynku komercyjnym](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Dowiedz się więcej o [środowiskach zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
- [Wyświetlanie klientów i zarządzanie nimi,](view-manage-customers.md) przechodząc do **witryny Moi klienci** w witrynie Azure portal.
