---
title: Usługa Azure Lighthouse w scenariuszach dla przedsiębiorstw
description: Możliwości latarni morskiej platformy Azure mogą służyć do uproszczenia zarządzania między dzierżawcami w przedsiębiorstwie, które używa wielu dzierżaw usługi Azure AD.
ms.date: 09/25/2019
ms.topic: conceptual
ms.openlocfilehash: 91089a6fb1a965191489e87027ef508c7ebe2aa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749210"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Usługa Azure Lighthouse w scenariuszach dla przedsiębiorstw

Najbardziej typowym scenariuszem dla [latarni morskiej platformy Azure](../overview.md) jest dostawca usług zarządzający zasobami w dzierżawach usługi Azure Active Directory (Azure AD) swoich klientów. Jednak możliwości latarni morskiej platformy Azure mogą być również używane do uproszczenia zarządzania między dzierżawami w przedsiębiorstwie, które używa wielu dzierżaw usługi Azure AD.

## <a name="single-vs-multiple-tenants"></a>Pojedynczy a wielu dzierżawców

W przypadku większości organizacji zarządzanie jest łatwiejsze w przypadku jednej dzierżawy usługi Azure AD. Posiadanie wszystkich zasobów w ramach jednej dzierżawy umożliwia centralizację zadań zarządzania przez wyznaczonych użytkowników, grupy użytkowników lub jednostki usługi w ramach tej dzierżawy. Zalecamy używanie jednej dzierżawy dla organizacji, gdy tylko jest to możliwe.

W tym samym czasie istnieją sytuacje, które mogą wymagać organizacji do obsługi wielu dzierżaw usługi Azure AD. W niektórych przypadkach może to być sytuacja tymczasowa, jak wtedy, gdy miały miejsce przejęcia i długoterminowa strategia konsolidacji dzierżawy zajmie trochę czasu, aby zdefiniować. Organizacja może również wymagać utrzymania wielu najemców na bieżąco (ze względu na całkowicie niezależne jednostki zależne, wymagania geograficzne lub prawne itd.). W przypadkach, gdy wymagana jest architektura z wieloma dzierżawami, zarządzanie zasobami delegowanymi platformy Azure może służyć do scentralizowania i usprawniania operacji zarządzania. Subskrypcje od wielu dzierżaw mogą być dołączane do [zarządzania zasobami delegowanymi platformy Azure,](azure-delegated-resource-management.md)umożliwiając wyznaczonym użytkownikom w dzierżawie zarządzającej wykonywanie [funkcji zarządzania między dzierżawcami](cross-tenant-management-experience.md) w sposób scentralizowany i skalowalny.

## <a name="tenant-management-architecture"></a>Architektura zarządzania dzierżawcami

Podczas centralizacji operacji zarządzania w wielu dzierżawach, należy określić, która dzierżawa będzie zawierać użytkowników wykonujących operacje zarządzania dla innych dzierżaw. Innymi słowy należy określić, który dzierżawca będzie dzierżawy zarządzania dla innych dzierżawców.

Załóżmy na przykład, że twoja organizacja ma jedną dzierżawę, którą wywołamy *w ujmijce A.* Następnie organizacja nabywa dwóch dodatkowych dzierżaw, *dzierżawy B* i *dzierżawy C*, i masz powody biznesowe, które wymagają, aby zachować je jako oddzielnych dzierżaw.

Organizacja chce używać tych samych definicji zasad, praktyk tworzenia kopii zapasowych i procesów zabezpieczeń we wszystkich dzierżawach. Ponieważ masz już użytkowników (w tym grupy użytkowników i podmioty usługi), którzy są odpowiedzialni za wykonywanie tych zadań w ramach dzierżawy A, można dołączać wszystkie subskrypcje w dzierżawie B i dzierżawy C, aby ci sami użytkownicy w dzierżawie A mogli je wykonywać. Zadania.

![Użytkownicy w dzierżawie A zarządzanie zasobami w dzierżawie B i dzierżawie C](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Zagadnienia dotyczące zabezpieczeń i dostępu

W większości scenariuszy przedsiębiorstwa należy delegować pełną subskrypcję dla zarządzania zasobami delegowanymi platformy Azure, chociaż można również delegować tylko określone grupy zasobów w ramach subskrypcji.

Tak czy inaczej, należy [przestrzegać zasady najmniejszych uprawnień przy definiowaniu, którzy użytkownicy będą mieli dostęp do zasobów.](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege) Pomaga to zapewnić, że użytkownicy mają tylko uprawnienia potrzebne do wykonywania wymaganych zadań i zmniejsza ryzyko przypadkowych błędów.

Zarządzanie zasobami delegowanymi usługami Azure Lighthouse i azure zapewniają tylko logiczne łącza między dzierżawą zarządzającą a zarządzanymi dzierżawcami, a nie fizycznie przenoszące dane lub zasoby. Ponadto dostęp zawsze idzie w jednym kierunku, od dzierżawy zarządzającej do zarządzanych dzierżaw.  Użytkownicy i grupy w dzierżawie zarządzającej należy nadal używać uwierzytelniania wieloskładnikowego podczas wykonywania operacji zarządzania na zarządzanych zasobów dzierżawy.

Przedsiębiorstwa z wewnętrznym lub zewnętrznym zarządzaniem i barierkami zgodności mogą używać [dzienników aktywności platformy Azure,](../../azure-monitor/platform/platform-logs-overview.md) aby spełnić wymagania dotyczące przejrzystości. Gdy dzierżawcy przedsiębiorstwa ustanowili relacje zarządzania i zarządzanych dzierżawców, użytkownicy w każdej dzierżawie mogą monitorować i uzyskiwać wgląd w akcje podejmowane przez użytkowników w innej dzierżawie, wyświetlając zarejestrowane działanie.

## <a name="onboarding-process-considerations"></a>Zagadnienia dotyczące procesu dołączania

Subskrypcje (lub grupy zasobów w ramach subskrypcji) można dołączać do zarządzania zasobami delegowanymi platformy Azure przez wdrożenie szablonów usługi Azure Resource Manager lub za pośrednictwem ofert usług zarządzanych opublikowanych w witrynie Azure Marketplace, prywatnie lub prywatnie lub Publicznie.

Ponieważ użytkownicy korporacyjni zwykle będą mogli uzyskać bezpośredni dostęp do dzierżaw przedsiębiorstwa i nie ma potrzeby sprzedawania ani promowania oferty zarządzania, wdrożenie jest zazwyczaj szybsze i prostsze do wdrożenia bezpośrednio za pomocą szablonów usługi Azure Resource Manager. Podczas gdy odnosimy się do dostawców usług i klientów w [wytycznych dotyczących dołączania,](../how-to/onboard-customer.md)przedsiębiorstwa mogą korzystać z tych samych procesów.

Jeśli wolisz, dzierżawcy w przedsiębiorstwie mogą być dołączane przez [opublikowanie oferty usług zarządzanych do portalu Azure Marketplace.](../how-to/publish-managed-services-offers.md) Aby upewnić się, że oferta jest dostępna tylko dla odpowiednich dzierżawców, upewnij się, że twoje plany są oznaczone jako prywatne. Za pomocą planu prywatnego można podać identyfikatory subskrypcji dla każdej dzierżawy, która ma być zaplanowana na pokładzie, a nikt inny nie będzie mógł uzyskać twojej oferty.

## <a name="terminology-notes"></a>Uwagi dotyczące terminologii

W przypadku zarządzania między dzierżawcami w przedsiębiorstwie odwołania do dostawców usług w dokumentacji usługi Azure Lighthouse można rozumieć jako mające zastosowanie do dzierżawy zarządzającej w przedsiębiorstwie — czyli dzierżawy, która obejmuje użytkowników, którzy będą zarządzać zasobami w innych dzierżawach za pośrednictwem platformy Azure delegowanego zarządzania zasobami. Podobnie odwołania do klientów można rozumieć, aby zastosować do dzierżaw, które są delegowania zasobów, które mają być zarządzane za pośrednictwem użytkowników w dzierżawie zarządzania.

Na przykład w przykładzie opisanym powyżej dzierżawy A można traktować jako dzierżawy dostawcy usług (dzierżawy zarządzania) i dzierżawy B i dzierżawy C można traktować jako dzierżawców klienta.

W tym przykładzie dzierżawy użytkownicy z odpowiednimi uprawnieniami mogą [wyświetlać delegowane zasoby i zarządzać nimi](../how-to/view-manage-customers.md) na stronie Moi **klienci** w witrynie Azure portal. Podobnie użytkownicy dzierżawy B i dzierżawy C z odpowiednimi uprawnieniami mogą [wyświetlać zasoby, które zostały delegowane](../how-to/view-manage-service-providers.md) do dzierżawy A i zarządzać nimi na stronie **Dostawcy usług** w witrynie Azure portal.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [środowiskach zarządzania między dzierżawcami](cross-tenant-management-experience.md).
- Dowiedz się więcej o [zarządzaniu zasobami delegowanymi na platformie Azure](azure-delegated-resource-management.md).