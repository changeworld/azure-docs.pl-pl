---
title: Zarządzanie tożsamościami — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Zarządzanie tożsamościami usługi Azure Active Directory umożliwia zrównoważenie zapotrzebowania organizacji na bezpieczeństwo i produktywność pracowników z odpowiednimi procesami i widocznością.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd319dd6a83a392f6df26d07a58be22a9c8bdb61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063683"
---
# <a name="what-is-azure-ad-identity-governance"></a>Co to jest usługa Azure AD Identity Governance?

Zarządzanie tożsamościami usługi Azure Active Directory (Azure AD) umożliwia zrównoważenie zapotrzebowania organizacji na bezpieczeństwo i produktywność pracowników z odpowiednimi procesami i widocznością. Zapewnia możliwości, aby zapewnić właściwym ludziom odpowiedni dostęp do odpowiednich zasobów. Te i powiązane funkcje usługi Azure AD i Enterprise Mobility + Security umożliwiają ograniczenie ryzyka dostępu poprzez ochronę, monitorowanie i inspekcję dostępu do krytycznych zasobów — przy jednoczesnym zapewnieniu produktywności pracowników i partnerów biznesowych.  

Zarządzanie tożsamościami daje organizacjom możliwość wykonywania następujących zadań między pracownikami, partnerami biznesowymi i dostawcami oraz między usługami i aplikacjami zarówno w środowisku lokalnym, jak i w chmurach:

- Zarządzanie cyklem życia tożsamości
- Zarządzanie cyklem życia dostępu
- Bezpieczny uprzywilejowany dostęp do administracji

W szczególności ma na celu pomóc organizacjom w rozwiązaniu tych czterech kluczowych pytań:

- Którzy użytkownicy powinni mieć dostęp do tych zasobów?
- Co ci użytkownicy robią z tym dostępem?
- Czy istnieją skuteczne mechanizmy kontroli organizacyjnej zarządzania dostępem?
- Czy audytorzy mogą sprawdzić, czy kontrole działają?

## <a name="identity-lifecycle"></a>Cykl życia tożsamości

Zarządzanie tożsamościami pomaga organizacjom osiągnąć równowagę między *wydajnością* — jak szybko dana osoba może mieć dostęp do potrzebnych im zasobów, na przykład po dołączeniu do mojej organizacji? I *bezpieczeństwo* - Jak ich dostęp powinien się zmieniać w czasie, na przykład ze względu na zmiany statusu zatrudnienia tej osoby?  Zarządzanie cyklem życia tożsamości jest podstawą zarządzania tożsamościami, a skuteczne zarządzanie na dużą skalę wymaga modernizacji infrastruktury zarządzania cyklem życia tożsamości dla aplikacji.

![Cykl życia tożsamości](./media/identity-governance-overview/identity-lifecycle.png)

Dla wielu organizacji cykl życia tożsamości pracowników jest powiązany z reprezentacją tego użytkownika w systemie HCM (zarządzanie kapitałem ludzkim).  Usługa Azure AD Premium automatycznie przechowuje tożsamości użytkowników dla osób reprezentowanych w workday zarówno w usłudze Active Directory, jak i usłudze Azure Active Directory, zgodnie z opisem w [samouczku inicjowania obsługi administracyjnej w ciągu dnia roboczego.](../saas-apps/workday-inbound-tutorial.md)  Usługa Azure AD Premium obejmuje również [usługę Microsoft Identity Manager,](/microsoft-identity-manager/)która może importować rekordy z lokalnych systemów HCM, takich jak SAP, Oracle eBusiness i Oracle PeopleSoft.

Coraz częściej scenariusze wymagają współpracy z osobami spoza organizacji. Współpraca [usługi Azure AD B2B](/azure/active-directory/b2b/) umożliwia bezpieczne udostępnianie aplikacji i usług organizacji użytkownikom-gościom i partnerom zewnętrznym z dowolnej organizacji, przy jednoczesnym zachowaniu kontroli nad własnymi danymi firmowymi.  [Zarządzanie uprawnieniami usługi Azure AD](entitlement-management-overview.md) umożliwia wybranie użytkowników organizacji, którzy mogą żądać dostępu i być dodawane jako goście B2B do katalogu organizacji i zapewnia, że ci goście są usuwani, gdy nie potrzebują już dostępu.

## <a name="access-lifecycle"></a>Dostęp do cyklu życia

Organizacje potrzebują procesu do zarządzania dostępem poza to, co zostało początkowo aprowied dla użytkownika, gdy tożsamość tego użytkownika została utworzona.  Ponadto organizacje przedsiębiorstw muszą być w stanie efektywnie skalować, aby móc na bieżąco rozwijać i egzekwować zasady dostępu i kontrole.

![Dostęp do cyklu życia](./media/identity-governance-overview/access-lifecycle.png)

Zazwyczaj IT deleguje decyzje o zatwierdzeniu dostępu do decydentów biznesowych.  Ponadto it może zaangażować samych użytkowników.  Na przykład użytkownicy, którzy uzyskują dostęp do poufnych danych klientów w aplikacji marketingowej firmy w Europie, muszą znać zasady firmy. Użytkownicy-goście mogą nie być świadomi wymagań dotyczących obsługi danych w organizacji, do której zostali zaproszeni.

Organizacje mogą zautomatyzować proces cyklu dostępu za pomocą technologii, takich jak [grupy dynamiczne,](../users-groups-roles/groups-dynamic-membership.md)w połączeniu z inicjacją obsługi administracyjnej użytkowników [aplikacji SaaS](../saas-apps/tutorial-list.md) lub [aplikacji zintegrowanych z scim](../app-provisioning/use-scim-to-provision-users-and-groups.md).  Organizacje mogą również kontrolować, którzy [użytkownicy-goście mają dostęp do aplikacji lokalnych.](../b2b/hybrid-cloud-to-on-premises.md)  Te prawa dostępu można następnie regularnie przeglądać za pomocą cyklicznych [przeglądów dostępu usługi Azure AD.](access-reviews-overview.md)   [Zarządzanie uprawnieniami usługi Azure AD](entitlement-management-overview.md) umożliwia również zdefiniowanie sposobu, w jaki użytkownicy żądają dostępu między pakietami członkostwa w grupach i zespołach, rolami aplikacji i rolami usługi SharePoint Online.

Gdy użytkownik próbuje uzyskać dostęp do aplikacji, usługa Azure AD wymusza zasady [dostępu warunkowego.](/azure/active-directory/conditional-access/) Na przykład zasady dostępu [warunkowego](../conditional-access/terms-of-use.md) mogą obejmować wyświetlanie warunków użytkowania i [upewnienie się, że użytkownik zgodził się na te warunki](../conditional-access/require-tou.md) przed możliwością uzyskania dostępu do aplikacji.

## <a name="privileged-access-lifecycle"></a>Cykl życia dostępu uprzywilejowanego

Historycznie uprzywilejowany dostęp został opisany przez innych dostawców jako odrębna możliwość od zarządzania tożsamościami. Jednak w firmie Microsoft uważamy, że zarządzanie uprzywilejowanym dostępem jest kluczową częścią zarządzania tożsamościami — zwłaszcza biorąc pod uwagę możliwość niewłaściwego wykorzystania skojarzonego z tymi prawami administratora, które może spowodować dla organizacji. Pracownicy, dostawcy i wykonawcy, którzy korzystają z praw administracyjnych, muszą być regulowani.

![Cykl życia dostępu uprzywilejowanego](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Usługa Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) zapewnia dodatkowe formanty dostosowane do zabezpieczania praw dostępu dla zasobów w usługach Azure AD, azure i innych usługach online firmy Microsoft.  Dostęp just-in-time i funkcje alertów zmiany ról udostępniane przez usługę Azure AD PIM, oprócz uwierzytelniania wieloskładnikowego i dostępu warunkowego, zapewniają kompleksowy zestaw kontroli nadzoru, aby pomóc w zabezpieczeniu zasobów firmy (katalogu, Role zasobów usługi Office 365 i platformy Azure). Podobnie jak w przypadku innych form dostępu, organizacje mogą używać przeglądów dostępu do konfigurowania ponownej certyfikacji dostępu cyklicznego dla wszystkich użytkowników w rolach administratora.

## <a name="getting-started"></a>Wprowadzenie

Zapoznaj się z wprowadzeniem karty **Zarządzania tożsamościami** w witrynie Azure portal, aby rozpocząć korzystanie z zarządzania uprawnieniami, przeglądów dostępu, zarządzania tożsamościami uprzywilejowanymi i warunków użytkowania.

![Rozpoczęcie zarządzania tożsamościami](./media/identity-governance-overview/getting-started.png)


Jeśli masz jakieś opinie na temat funkcji zarządzania tożsamościami, kliknij pozycję **Masz opinię** w witrynie Azure portal, aby przesłać opinię. Zespół regularnie przegląda Twoją opinię.

Chociaż nie ma idealnego rozwiązania ani zalecenia dla każdego klienta, poniższe przewodniki konfiguracji zawierają również podstawowe zasady zalecane przez firmę Microsoft, aby zapewnić bezpieczniejszą i wydajną siłę roboczą.

- [Konfiguracje obsługi tożsamości i uzyskiwania dostępu do urządzeń](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Zabezpieczanie dostępu uprzywilejowanego](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Dodatek — najmniej uprzywilejowane role do zarządzania funkcjami zarządzania tożsamościami

Najlepszym rozwiązaniem jest używanie roli najmniej uprzywilejowanej do wykonywania zadań administracyjnych w zarządzaniu tożsamościami. Zaleca się użycie usługi Azure AD PIM do aktywowania roli zgodnie z potrzebami do wykonywania tych zadań. Poniżej przedstawiono najmniej uprzywilejowane role katalogu do skonfigurowania funkcji zarządzania tożsamościami:

| Funkcja | Najmniej uprzywilejowana rola |
| ------- | --------------------- |
| Zarządzanie upoważnieniami | Administrator użytkownika (z wyjątkiem dodawania witryn usługi SharePoint Online do katalogów, co wymaga administratora globalnego) |
| Przeglądy dostępu | Administrator użytkownika (z wyjątkiem przeglądów dostępu ról platformy Azure lub usługi Azure AD, która wymaga administratora ról uprzywilejowanych) |
|Privileged Identity Management | Administrator ról uprzywilejowanych |
| Warunki użytkowania | Administrator zabezpieczeń lub administrator dostępu warunkowego |

## <a name="next-steps"></a>Następne kroki

- [Co to jest zarządzanie upoważnieniami w usłudze Azure AD?](entitlement-management-overview.md)
- [Co to są przeglądy dostępu usługi Azure AD?](access-reviews-overview.md)
- [Co to jest usługa Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Do czego można wykorzystać warunki użytkowania?](active-directory-tou.md)


