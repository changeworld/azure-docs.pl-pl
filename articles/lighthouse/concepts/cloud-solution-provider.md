---
title: Kekenis platformy Azure i programu Cloud Solution Provider
description: Podczas korzystania z systemu Azure delegowane zarządzanie zasobami, warto wziąć pod uwagę zabezpieczeń i kontroli dostępu.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 09552c66d2dc841cff8b5cb52e26dc657568e08f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809960"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Kekenis platformy Azure i programu Cloud Solution Provider

Jeśli jesteś [CSP (dostawca rozwiązań w chmurze)](https://docs.microsoft.com/partner-center/csp-overview) partnera, możesz uzyskać dostęp subskrypcji platformy Azure utworzone dla swoich klientów za pośrednictwem programu CSP przy użyciu [Administruj w imieniu Of (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) funkcji. Ten dostęp umożliwia bezpośrednio obsługuje, konfigurowanie i zarządzanie subskrypcjami klientów.

Mechanizm AOBO daje pełny dostęp do środowiska klienta. Za pomocą usługi Azure resource delegowanego management wraz z AOBO pomaga zwiększyć bezpieczeństwo, umożliwiając Ogranicz dostęp niepotrzebne, należy włączyć bardziej szczegółowych uprawnień dla użytkowników. 

## <a name="administer-on-behalf-of-aobo"></a>Administruj w imieniu-of (AOBO)

Za pomocą AOBO, każdy użytkownik mający [Agent administracyjny](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) roli w ramach dzierżawy będą mieć dostęp AOBO do subskrypcji platformy Azure utworzone za pomocą programu CSP. Użytkownicy, którzy potrzebują dostępu do żadnych klientów subskrypcji musi należeć do tej grupy. AOBO nie zezwala na elastyczność, aby utworzyć różne grupy, które działają z różnych klientów lub włączyć przypisane różne role dla grup lub użytkowników.

![Zarządzanie dzierżawą przy użyciu AOBO](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Zarządzanie zasobami platformy Azure delegowanego

Korzystanie z platformy Azure delegowane zarządzanie zasobami, można przypisać różne grupy różnych klientów lub ról, jak pokazano na poniższym diagramie. Ponieważ użytkownicy będą mieć odpowiedni poziom dostępu za pośrednictwem usługi Azure resource delegowanego management, można zmniejszyć liczbę użytkowników, którzy mieć rolę administratora agenta (i w związku z tym mają pełny dostęp AOBO). To zapewnia większe bezpieczeństwo, ograniczając niepotrzebne dostęp do zasobów Twoich klientów. Oferuje on również większą elastyczność i możliwość zarządzania wieloma klientami na dużą skalę.

Dołączanie subskrypcji, który został utworzony za pomocą programu CSP następuje wykonanie kroków opisanych w [dodać subskrypcję platformy Azure delegowane zarządzanie zasobami](../how-to/onboard-customer.md). Każdy użytkownik mający rolę administratora agenta w dzierżawie można wykonać tego dołączania.

Należy pamiętać, że w przypadku subskrypcji utworzonych za pomocą programów CSP, tylko można wygenerować żądania pomocy technicznej przez użytkowników z rolą administratora agenta w dzierżawie dostawcy usług. Użytkownicy dodani za pomocą usługi Azure resource delegowanego management nie będzie mógł otworzyć żądania pomocy technicznej dla delegowanego zasobów w tych subskrypcjach.

![Zarządzanie dzierżawą przy użyciu AOBO i Azure delegowane zarządzanie zasobami](../media/csp-2.jpg)

## <a name="partner-admin-link"></a>Łącze administrator partnera

Identyfikator sieci Microsoft Partner Network (MPN) można skojarzyć z subskrypcjami dołączone do śledzenia Twojej wpływu na podczas pracy z klientami.

Jeśli użytkownik [publikowanie oferty usług zarządzanych w portalu Azure Marketplace](../how-to/publish-managed-services-offers.md), swój identyfikator MPN jest skojarzone z danym profilem wydawcy i jest automatycznie kojarzony z tej oferty. Przychodów generowanych przez zasoby platformy Azure za pośrednictwem tej oferty będą następnie opartego na atrybutach dla Twojej organizacji. W obrębie partnera zgłoszenie systemów takich jak Centrum partnerskiego lub MPN uznanie autorstwa pojawi się łącze administratora jako Partner (PAL).

Jeśli użytkownik [angażowania nowych klientów platformy Azure delegowane zarządzanie zasobami za pomocą szablonów usługi Azure Resource Manager](../how-to/onboard-customer.md), nadal można skojarzyć swój identyfikator MPN w celu odbierania rozpoznawania dla Twojego wpływ na podczas pracy z klientami, ale będzie miał to zrobić ręcznie. Aby uzyskać więcej informacji, zobacz [połączyć Identyfikatora partnera kont systemu Azure](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started). 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [międzydzierżawowa środowiska zarządzania](cross-tenant-management-experience.md).
- Dowiedz się więcej o [programu Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview).
