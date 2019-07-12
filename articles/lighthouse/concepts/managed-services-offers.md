---
title: Zarządzane oferty usług w witrynie Azure Marketplace
description: Zarządzane usługi, które oferuje umożliwiają dostawcom usług sprzedaży zasobów zarządzania ofert dla klientów w witrynie Azure Marketplace.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: a6fcf5f1d0ac194d60f834fb8d26db019c538410
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809876"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Zarządzane oferty usług w witrynie Azure Marketplace

W tym artykule opisano nowe **usług zarządzanych** typ w oferty [portalu Azure Marketplace](https://azuremarketplace.microsoft.com). Zarządzane usługi, które oferuje pozwalają klientom usług zarządzania zasobów za pomocą funkcji zarządzania usługi Azure resource delegowanego. Możesz udostępnić te oferty do wszystkich potencjalnych klientów lub tylko do jednego lub więcej określonych odbiorców. Ponieważ obciążana płatnościami za klientów bezpośrednio koszty związane z tych usług zarządzanych, nie istnieją żadne opłaty naliczane przez firmę Microsoft.

## <a name="understand-managed-services-offers"></a>Zrozumienie oferty usług zarządzanych

Oferty usług zarządzanych usprawnić proces dołączania klientów dla usługi Azure delegowane zarządzanie zasobami. Gdy klient zakupi oferty w portalu Azure Marketplace, będziesz mieć możliwość subskrypcji i/lub grup zasobów powinien być dołączona tak, aby określić użytkowników w organizacji mogą wykonywać zadania administracyjne dla klienta z poziomu usługi dzierżawy organizacji.

Po tym żadne dalsze działania jest wymagane przez klienta lub dostawcy usług umożliwiające rejestrację klienta. Jest to spowodowane podczas definiowania oferty w [portalu Cloud Partner](https://cloudpartner.azure.com/), tworzenie manifestu, który określa użytkowników usługi Azure AD, grup i zasad usługi, które będą miały dostęp do zasobów klientów korzystających z platformy Azure delegowane zasobów Zarządzanie. wraz z ról, które określają poziom dostępu. Przypisując uprawnienia do grupy usługi Azure AD, a nie szereg poszczególnych użytkowników i kont aplikacji, można dodawać lub usuwać pojedynczych użytkowników, gdy zmienią się Twoje wymagania dotyczące dostępu.

## <a name="public-and-private-offers"></a>Oferty publicznych i prywatnych

Każda oferta usługi zarządzane zawiera co najmniej jeden plan. Te plany mogą być prywatnej lub publicznej.

Jeśli chcesz ograniczyć oferty do konkretnych klientów, możesz opublikować planu prywatnych. Jeśli tak zrobisz, plan można kupić tylko dla konkretnych] identyfikatory, które należy podać subskrypcji. Aby uzyskać więcej informacji, zobacz [oferty prywatne](https://docs.microsoft.com/azure/marketplace/private-offers).

Plany publiczne pozwalają podwyższanie poziomu usług dla nowych klientów. Są to zazwyczaj bardziej odpowiednie, gdy jest potrzebna tylko ograniczony dostęp do dzierżawy klienta. Po utworzeniu relacji z klientem, jeśli postanowią do udzielania dostępu dodatkowe Twojej organizacji, możesz to zrobić poprzez publikowanie nowy plan prywatnej tylko tego klienta lub przez [dołączania ich do dalszego dostępu za pomocą platformy Azure Szablony usługi Resource Manager](../how-to/onboard-customer.md).

Należy pamiętać o tym, gdy plan został opublikowany jako publiczne, nie możesz zmienić ją na prywatną. Ponadto nie można ograniczyć dostępność publiczny plan dla niektórych klientów, a nawet pewną liczbę klientów, mimo że można zatrzymać całkowicie sprzedaży planie, jeśli chcesz to zrobić.

Jeśli to stosowne, można dołączyć prywatnych i publicznych planów w ramach tej samej oferty.

## <a name="publish-managed-service-offers"></a>Publikowanie oferty usług zarządzanych

Aby dowiedzieć się, jak opublikować ofertę usług zarządzanych, zobacz [publikowanie oferty usług zarządzanych w portalu Azure Marketplace](../how-to/publish-managed-services-offers.md). Aby uzyskać ogólne informacje na temat publikowania w portalu Azure Marketplace przy użyciu portalu Cloud Partner, zobacz [portalu Azure Marketplace i AppSource Podręcznik publikowania](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) i [zarządzania platformy Azure i usługi AppSource w portalu Marketplace oferty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [Azure delegowane zarządzanie zasobami](azure-delegated-resource-management.md) i [międzydzierżawowa środowiska zarządzania](cross-tenant-management-experience.md).
- [Publikowanie oferty usług zarządzanych](../how-to/publish-managed-services-offers.md) portalu Azure Marketplace.
