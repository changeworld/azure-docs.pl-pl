---
title: Zarządzanie tożsamościami — usługa Azure Active Directory | Dokumentacja firmy Microsoft
description: Usługa Azure Active Directory Zarządzanie tożsamościami oraz umożliwia zrównoważenia organizacji na potrzeby zabezpieczeń i pracownikom produktywność dzięki odpowiednie procesy i widoczności.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/29/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b4f1563aa0437cd45c297b95a83119318a24624
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109587"
---
# <a name="what-is-azure-ad-identity-governance"></a>Co to jest zarządzanie usługi Azure AD Identity?

Zarządzanie tożsamościami w usłudze Azure Active Directory (Azure AD) umożliwia zrównoważenia organizacji na potrzeby zabezpieczeń i pracownikom produktywność dzięki odpowiednie procesy i widoczności. Udostępnia możliwości, aby upewnić się, że odpowiednie użytkownicy mają prawo dostępu do odpowiednich zasobów i pozwala na ochronę, monitorować i sprawdzać uzyskać dostęp do krytycznych zasobów--przy jednoczesnym zapewnieniu produktywność pracowników.  

Zarządzanie tożsamościami oraz zapewnić organizacjom zdolności do wykonywania następujących zadań różnych pracowników, partnerów biznesowych oraz dostawców i usługach i aplikacjach:

- Zarządzanie cyklem życia tożsamości
- Zarządzanie cyklem życia dostępu
- Bezpieczne administrowanie

W szczególności jest przeznaczony ma pomóc organizacjom rozwiązać te cztery kluczowe pytania:

- Użytkowników, którzy powinni mieć dostęp do określonych zasobów?
- Co robią tych użytkowników z tym dostępem?
- Czy istnieją efektywne procedury nadzoru organizacji w celu zarządzania dostępem?
- Audytorzy sprawdzić, czy formanty działają?

## <a name="identity-lifecycle"></a>Cykl życia tożsamości

Zarządzanie tożsamościami oraz pomaga organizacjom osiągać równowagę między *produktywność* — jak szybko można osoby mają dostęp do zasobów potrzebują, np. gdy zostają dołączone w mojej organizacji? I *zabezpieczeń* — jak należy ich dostęp zmienić wraz z upływem czasu, takich jak z powodu zmian na status zatrudnienia osoby?  Zarządzanie cyklem życia tożsamości jest podstawą dla zarządzania tożsamościami i skuteczne nadzór w odpowiedniej skali wymaga modernizacji infrastruktury zarządzania cyklem życia tożsamości dla aplikacji.

![Cykl życia tożsamości](./media/identity-governance-overview/identity-lifecycle.png)

W przypadku wielu organizacji cyklem życia tożsamości dla pracowników jest powiązany do reprezentacji tego użytkownika w systemie HCM (Zarządzanie kapitałem ludzkim).  Usługa Azure AD Premium również automatycznie obsługuje tożsamości użytkowników dla osób reprezentowane w produkcie Workday w usługi Active Directory i Azure Active Directory, zgodnie z opisem w [Workday samouczek provisioning (wersja zapoznawcza) dla ruchu przychodzącego](../saas-apps/workday-inbound-tutorial.md).  Usługa Azure AD Premium obejmuje również [programu Microsoft Identity Manager](/microsoft-identity-manager/), który można zaimportować rekordy z lokalnych systemów HCM, takich jak SAP, Oracle eBusiness i Oracle PeopleSoft.

Coraz większym stopniu scenariusze wymagają współpracy z osobami spoza organizacji. [Usługa Azure AD B2B](/azure/active-directory/b2b/) współpracy pozwala na bezpieczne udostępnianie aplikacji i usług Twojej organizacji za pomocą użytkowników-gości i partnerom zewnętrznym w każdej organizacji, przy zachowaniu kontroli nad danych firmowych.

## <a name="access-lifecycle"></a>Cykl życia dostępu

Organizacje wymagają procesu do zarządzania dostępem poza co początkowo udostępniony dla użytkownika podczas tworzenia tej tożsamości użytkownika.  Ponadto przedsiębiorstwa to organizacje muszą mieć możliwość skalowania efektywnie, aby można było tworzenie i wymuszanie zasad dostępu i kontroli w sposób ciągły.

![Cykl życia dostępu](./media/identity-governance-overview/access-lifecycle.png)

Zazwyczaj IT delegatów dostępu zatwierdzeniu podjęte decyzje w kwestii osobom podejmującym decyzje biznesowe.  Ponadto IT może obejmować samych użytkowników.  Na przykład trzeba znać zasady firmy użytkowników uzyskujących dostęp do danych poufnych klientów w aplikacji marketingowych firmy w Europie. Użytkownicy-goście mogą jest niebranie pod uwagę wymagania dotyczące przetwarzania danych w organizacji, do której zostali zaproszeni.

Organizacje takie jak zautomatyzować proces cyklu życia dostępu za pomocą technologii [grup dynamicznych](../users-groups-roles/groups-dynamic-membership.md), powiązanych z aprowizacji użytkowników do [aplikacji SaaS](../saas-apps/tutorial-list.md) lub [aplikacje zintegrować przy użyciu SCIM](../manage-apps/use-scim-to-provision-users-and-groups.md).  Organizacje mogą również kontrolować, które [użytkownicy-goście mają dostęp do aplikacji lokalnych](../b2b/hybrid-cloud-to-on-premises.md).  Te mogą praw dostępu, a następnie być regularnie analizowane za pomocą cykliczne [przeglądy dostępu w usłudze Azure AD](access-reviews-overview.md).

Gdy użytkownik próbuje uzyskać dostęp do aplikacji, Azure AD wymusza [dostępu warunkowego](/azure/active-directory/conditional-access/) zasad. Na przykład zasady dostępu warunkowego obejmują wyświetlanie [warunki użytkowania](../conditional-access/terms-of-use.md) i [zapewnienie użytkownik zgodził się na te warunki](../conditional-access/require-tou.md) przed uzyskaniem dostępu do aplikacji.

## <a name="privileged-access-lifecycle"></a>Cykl życia uprzywilejowanego dostępu

W przeszłości uprzywilejowanego dostępu został opisany przez innych dostawców jako osobne możliwości od zarządzania tożsamościami. Jednak w firmie Microsoft uważamy regulujące uprzywilejowanego dostępu jest kluczowa część pakietu zarządzania tożsamościami oraz — szczególnie podane potencjał pod kątem nieprawidłowego użycia skojarzone z tych uprawnień może spowodować organizacji administratora. Pracownicy, dostawcy i wykonawców, które przejąć prawa administracyjne muszą podlegać.

![Cykl życia uprzywilejowanego dostępu](./media/identity-governance-overview/privileged-access-lifecycle.png)

Usługa Azure AD Privileged Identity Management (PIM) zapewnia dodatkową kontrolę, które dostosowane do zabezpieczania dostępu prawa do zasobów, w usłudze Azure AD platformy Azure i innych Microsoft Online Services.  Dostęp just in time i rolę zmienić alerty funkcjami oferowanymi przez usługi Azure AD PIM, oprócz uwierzytelniania wieloskładnikowego i dostępu warunkowego, zapewnić kompleksowy zestaw formantów nadzoru, aby ułatwić bezpiecznych zasobów firmy (katalog, Usługi Office 365, a role zasobów platformy Azure). Podobnie jak w przypadku innych form dostępu organizacje mogą używać przeglądów dostępu, aby skonfigurować cyklicznego wystawiły dostępu dla wszystkich użytkowników w rolach administratora.

## <a name="getting-started"></a>Wprowadzenie

Następujące konfiguracje oferują Przewodnik zasady linii bazowej, którą firma Microsoft zaleca wykonanie aby zapewnić większe bezpieczeństwo i produktywność pracowników, gdy nie ma znaleźć idealne rozwiązanie lub zalecenie dla każdego klienta.

- [Konfiguracje dostępu tożsamości i urządzenia](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Zabezpieczanie dostępu uprzywilejowanego](../users-groups-roles/directory-admin-roles-secure.md)

Możesz również zapoznać się na karcie wprowadzające **zarządzania tożsamościami oraz** w witrynie Azure portal, aby rozpocząć korzystanie z Zarządzanie uprawnieniami, dostęp do recenzji, Zarządzanie tożsamościami uprzywilejowanymi i warunki użytkowania.

![Zarządzanie tożsamościami oraz wprowadzenie](./media/identity-governance-overview/getting-started.png)

## <a name="next-steps"></a>Kolejne kroki

- [Co to jest zarządzanie uprawnieniami w usłudze Azure AD? (wersja zapoznawcza)](entitlement-management-overview.md)
- [Co to są przeglądy dostępu w usłudze Azure AD?](access-reviews-overview.md)
- [Co to jest Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Do czego można wykorzystać warunki użytkowania?](active-directory-tou.md)
