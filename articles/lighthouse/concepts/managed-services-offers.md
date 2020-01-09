---
title: Oferty usług zarządzanych w witrynie Azure Marketplace
description: Dzięki usługom zarządzanym dostawcy usług mogą sprzedawać oferty zarządzania zasobami klientom w portalu Azure Marketplace.
ms.date: 12/16/2019
ms.topic: conceptual
ms.openlocfilehash: 1b4f0d7457a74afe710a48f429cfe47535a9b122
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453584"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Oferty usług zarządzanych w witrynie Azure Marketplace

W tym artykule opisano typ oferty nowych **usług zarządzanych** w [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com). Oferty usług zarządzanych umożliwiają oferowanie klientom usług zarządzania zasobami przy użyciu delegowanych zasobów platformy Azure. Oferty te można udostępnić wszystkim potencjalnym klientom lub tylko jednemu lub wielu konkretnym klientom. Ze względu na to, że opłaty są naliczane bezpośrednio za koszty związane z tymi usługami zarządzanymi, firma Microsoft nie pobiera żadnych opłat.

## <a name="understand-managed-services-offers"></a>Opis ofert usług zarządzanych

Usługi zarządzane oferują uproszczony proces dołączania klientów do zarządzania zasobami delegowanymi przez platformę Azure. Gdy klient kupuje ofertę w portalu Azure Marketplace, będzie mógł określić, które subskrypcje i/lub grupy zasobów powinny zostać dołączone. Należy pamiętać, że subskrypcja musi być najpierw autoryzowana do dołączenia przez ręczne zarejestrowanie dostawcy zasobów **Microsoft. ManagedServices** .

Następnie użytkownicy w organizacji będą mogli wykonywać zadania administracyjne dotyczące tych zasobów z dzierżawy w organizacji, zgodnie z dostępem zdefiniowanym podczas tworzenia oferty w [Portal Cloud partner](https://cloudpartner.azure.com/). Jest to realizowane za pośrednictwem manifestu, który określa użytkowników usługi Azure AD, grupy i jednostki usługi, które będą miały dostęp do zasobów klienta przy użyciu funkcji zarządzania zasobami delegowanych przez platformę Azure, wraz z rolami, które definiują poziom dostępu. Przypisując uprawnienia do grupy usługi Azure AD, a nie serii poszczególnych kont użytkowników lub aplikacji, można dodawać lub usuwać poszczególnych użytkowników, gdy zmienią się wymagania dotyczące dostępu.

## <a name="public-and-private-offers"></a>Oferty publiczne i prywatne

Każda oferta usług zarządzanych obejmuje jeden lub więcej planów. Plany mogą być prywatne lub publiczne. 

Jeśli chcesz ograniczyć ofertę do określonych klientów, możesz opublikować plan prywatny. Po wykonaniu tej czynności plan można kupić tylko dla określonych identyfikatorów subskrypcji, które podano. Aby uzyskać więcej informacji, zobacz [prywatne oferty](../../marketplace/private-offers.md).

Plany publiczne umożliwiają podwyższenie poziomu usług do nowych klientów. Są one zazwyczaj bardziej odpowiednie, gdy wymagany jest tylko ograniczony dostęp do dzierżawy klienta. Po ustanowieniu relacji z klientem, jeśli zdecydują się udzielić dodatkowej dostępu do organizacji, możesz to zrobić przez opublikowanie nowego planu prywatnego tylko dla tego klienta lub przez dołączenie [ich do dodatkowego dostępu przy użyciu szablonów Azure Resource Manager](../how-to/onboard-customer.md).

W razie potrzeby można uwzględnić zarówno plany publiczne, jak i prywatne w tej samej ofercie.

> [!IMPORTANT]
> Po opublikowaniu planu jako publicznego nie można go zmienić na prywatny. Aby kontrolować, którzy klienci mogą akceptować ofertę i delegować zasoby, należy użyć planu prywatnego. W przypadku planu publicznego nie można ograniczyć dostępności do określonych klientów, a nawet do określonej liczby klientów (mimo że można już zrezygnować z sprzedaży planu w przypadku wybrania tej opcji). Obecnie nie ma żadnego mechanizmu odrzucania lub usuwania delegowania, gdy klient zaakceptuje ofertę, chociaż zawsze możesz skontaktować się z klientem i poproś o [usunięcie dostępu](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Publikuj oferty usługi zarządzanej

Aby dowiedzieć się, jak opublikować ofertę usług zarządzanych, zobacz temat [Publikowanie oferty usług zarządzanych w witrynie Azure Marketplace](../how-to/publish-managed-services-offers.md). Ogólne informacje o publikowaniu w portalu Azure Marketplace przy użyciu portal Cloud Partner można znaleźć w witrynie [Azure Marketplace i podręczniku publikowania AppSource](../../marketplace/marketplace-publishers-guide.md) oraz [zarządzać ofertami platformy Azure i AppSource Marketplace](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat funkcji [zarządzania zasobami delegowanymi przez platformę Azure](azure-delegated-resource-management.md) i [zarządzania wieloma dzierżawcami](cross-tenant-management-experience.md).
- [Publikowanie usług zarządzanych jest dostępne](../how-to/publish-managed-services-offers.md) w witrynie Azure Marketplace.
