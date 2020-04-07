---
title: Oferty usług zarządzanych w portalu Azure Marketplace
description: Oferty usług zarządzanych umożliwiają dostawcom usług sprzedawanie ofert zarządzania zasobami klientom w portalu Azure Marketplace.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: a0670bd74971132dcf243736bdf882a00154a942
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672420"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Oferty usług zarządzanych w portalu Azure Marketplace

W tym artykule opisano typ oferty **usługi zarządzanej** w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com). Oferty usług zarządzanych umożliwiają oferowanie klientom usług zarządzania zasobami przy użyciu [usługi Azure.](azure-delegated-resource-management.md) Możesz udostępnić te oferty wszystkim potencjalnym klientom lub tylko jednemu lub większej liczbie konkretnych klientów. Ponieważ klienci rozliczają bezpośrednio koszty związane z tymi usługami zarządzanymi, firma Microsoft nie pobiera żadnych opłat.

## <a name="understand-managed-service-offers"></a>Opis ofert usług zarządzanych

Usługa zarządzana oferuje usprawnienie procesu dołączania klientów do zarządzania zasobami delegowanymi platformy Azure. Gdy klient kupuje ofertę w portalu Azure Marketplace, będzie mógł określić, które subskrypcje i/lub grupy zasobów powinny być dołączane.

Następnie użytkownicy w organizacji będą mogli pracować nad tymi zasobami z poziomu dzierżawy organizacji, zgodnie z dostępem zdefiniowanym podczas tworzenia oferty w [portalu Cloud Partner Portal](https://cloudpartner.azure.com/). Odbywa się to za pośrednictwem manifestu, który określa użytkowników, grupy i jednostki usługi Azure AD, które będą miały dostęp do zasobów klienta przy użyciu usługi Azure zarządzanie zasobami delegowanymi, wraz z rolami, które definiują ich poziom dostępu. Przypisując uprawnienia do grupy usługi Azure AD, a nie do serii kont poszczególnych użytkowników lub aplikacji, można dodawać lub usuwać poszczególnych użytkowników po zmianie wymagań dotyczących dostępu.

## <a name="public-and-private-offers"></a>Oferty publiczne i prywatne

Każda oferta usług zarządzanych zawiera jeden lub więcej planów. Plany mogą być prywatne lub publiczne.

Jeśli chcesz ograniczyć ofertę do konkretnych klientów, możesz opublikować plan prywatny. Gdy to zrobisz, plan można kupić tylko dla określonych] identyfikatorów subskrypcji, które podasz. Aby uzyskać więcej informacji, zobacz [Oferty prywatne](../../marketplace/private-offers.md).

Plany publiczne umożliwiają promowanie usług wśród nowych klientów. Są one zwykle bardziej odpowiednie, gdy potrzebujesz tylko ograniczonego dostępu do dzierżawy klienta. Po nawiązaniu relacji z klientem, jeśli zdecydują się udzielić organizacji dodatkowego dostępu, można to zrobić, publikując nowy plan prywatny tylko dla tego klienta lub [dołączając go do dalszego dostępu przy użyciu szablonów usługi Azure Resource Manager.](../how-to/onboard-customer.md)

W razie potrzeby w tej samej ofercie można uwzględnić zarówno plany publiczne, jak i prywatne.

> [!IMPORTANT]
> Po opublikowaniu planu jako publicznego nie można go zmienić na prywatny. Aby kontrolować, którzy klienci mogą akceptować ofertę i delegować zasoby, użyj planu prywatnego. W przypadku planu publicznego nie można ograniczyć dostępności do niektórych klientów, a nawet do określonej liczby klientów (chociaż możesz całkowicie zaprzestać sprzedaży planu, jeśli to zrobisz). Dostęp [do delegowania](../how-to/onboard-customer.md#remove-access-to-a-delegation) można usunąć po zaakceptowaniu oferty przez klienta tylko wtedy, gdy podczas publikowania oferty została uwzględniona **autoryzacja** z **definicją roli** ustawioną na [Rolę usuwania przypisania rejestracji usług zarządzanych.](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) Możesz również skontaktować się z klientem i poprosić go o [usunięcie dostępu.](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)

## <a name="publish-managed-service-offers"></a>Publikowanie ofert usług zarządzanych

Aby dowiedzieć się, jak opublikować ofertę usług zarządzanych, zobacz [Publikowanie oferty usług zarządzanych w portalu Azure Marketplace](../how-to/publish-managed-services-offers.md). Aby uzyskać ogólne informacje na temat publikowania w portalu Azure Marketplace przy użyciu portalu cloud partner, zobacz [Przewodnik publikowania w portalu Azure Marketplace i AppSource](../../marketplace/marketplace-publishers-guide.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zarządzaniu zasobami delegowanymi platformy Azure](azure-delegated-resource-management.md) i [środowiskach zarządzania między dzierżawami.](cross-tenant-management-experience.md)
- [Publikuj oferty usług zarządzanych](../how-to/publish-managed-services-offers.md) w portalu Azure Marketplace.
