---
title: Zarządzanie kontami administracyjnymi awaryjnego dostępu w usłudze Azure AD | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób używania kont dostęp awaryjny ułatwiające organizacjom ograniczenie dostępu uprzywilejowanego w istniejącym środowisku usługi Azure Active Directory.
services: active-directory
author: markwahl-msft
ms.author: billmath
ms.date: 12/13/2017
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.openlocfilehash: 5c256befba4c73527b529c953f7b9cd4d266450c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30901109"
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>Zarządzanie kontami administracyjnymi awaryjnego dostępu w usłudze Azure AD 

Dla większości codziennych działań *administratora globalnego* praw nie są wymagane przez użytkowników. Użytkownicy powinna nie można trwale przypisać do roli, ponieważ przypadkowo wykonują zadania, które wymaga uprawnień wyższe niż powinny mieć. Gdy użytkownicy nie muszą pełnić rolę administratora globalnego, ich należy aktywować przypisania roli przy użyciu usługi Azure Active Directory (Azure AD) Privileged Identity zarządzania (PIM), na ich własnego konta lub alternatywne konta administracyjnego.

Oprócz użytkowników biorąc praw dostępu administracyjnego do siebie, należy uniemożliwić przypadkowo blokowanie poza administracji dzierżawy usługi Azure AD, ponieważ nie można zarejestrować ani aktywować konto istniejące poszczególnych użytkowników jako Administrator. Można zmniejszyć skuteczność przypadkowej braku dostępu administracyjnego przez zapisanie co najmniej dwóch *kont dostęp awaryjny* w dzierżawie.

Dostęp awaryjny konta pozwalają organizacjom ograniczenie dostępu uprzywilejowanego w istniejącym środowisku usługi Azure Active Directory. Takie konta są wysoce uprzywilejowane, a nie są przypisane do konkretnych osób. Dostęp awaryjny konta są ograniczone do awaryjnego lub "awaryjne scenariuszy sytuacji, gdy nie można użyć normalnego konta z uprawnieniami administracyjnymi. Organizacje muszą zachować celem ograniczenia się tylko do tego czasu, w którym konieczne jest użycie kont awaryjnych.

Organizacja może być konieczne użycie konta dostęp awaryjny w następujących sytuacjach:

 - Konta użytkowników są federacyjnych i federacyjnych jest obecnie niedostępna z powodu przerwania połączenia sieciowego komórki lub awarii dostawcy tożsamości. Na przykład jeśli host dostawcy tożsamości w danym środowisku uległy awarii, użytkownicy mogą mieć nie można zalogować się przypadku usługi Azure AD przekierowania do dostawcy tożsamości. 
 - Administratorzy są rejestrowane za pomocą usługi Azure Multi-Factor Authentication, a ich poszczególnych urządzeń są niedostępne. Użytkownicy mogą mieć nie może wykonać uwierzytelnianie wieloskładnikowe, aby aktywować rolę. Na przykład awaria sieci komórki uniemożliwia udzielenie odpowiedzi na połączenia telefoniczne lub odbieranie wiadomości tekstowych, które w zarejestrowani mechanizmów uwierzytelniania tylko dwa na swoim urządzeniu. 
 - Osoba z najnowszych globalnego dostępu administracyjnego opuścił organizację. Usługi Azure AD uniemożliwia ostatniego *administratora globalnego* konta przed usunięciem, ale nie uniemożliwia konto przed usunięciem lub wyłączone lokalnie. Albo sytuacji może być nie można odzyskać konta organizacji.

## <a name="initial-configuration"></a>Wstępna konfiguracja

Utwórz dwa lub więcej kont awaryjnych dostępu. Powinny być tylko w chmurze kont, które używają \*. onmicrosoft.com domeny i że nie są federacyjnych lub synchronizowane ze środowiska lokalnego. 

Konta nie powinna być skojarzona z dowolnym poszczególnych użytkowników w organizacji. Organizacje muszą także przeprowadzić upewnij się, że poświadczenia tego konta pozostają bezpieczne i znanych tylko do osób, które są autoryzowane do używania ich. 

> [!NOTE]
> Hasło do konta awaryjne dostępu są zwykle podzielone na dwie lub trzy części, zapisane w oddzielnych części dokumentu i przechowywane w bezpieczny, ognioodporną sejfów znajdujących się w bezpieczny, osobnych lokalizacji. 
>
> Upewnij się, że konta dostęp awaryjny nie są połączone z telefonów komórkowych wszelkie dostarczone przez pracowników, które są przesyłane z poszczególnych pracowników lub innych poświadczeń danego pracownika tokenów sprzętu. W ten sposób obejmuje pracownika jest nieosiągalne, gdy wymagane jest poświadczenie. 

### <a name="initial-configuration-with-permanent-assignments"></a>Wstępna konfiguracja z przypisania stałego

Jedną z opcji jest stałe członkami umożliwiający użytkownikowi *administratora globalnego* roli. Ta opcja będzie odpowiednia dla organizacji, które nie mają subskrypcji Azure AD Premium P2.

Aby zmniejszyć ryzyko ataku wynikające z naruszonymi zabezpieczeniami hasła, usługi Azure AD zaleca wymusić uwierzytelnianie wieloskładnikowe dla wszystkich poszczególnych użytkowników. Administratorzy i wszystkich innych (na przykład finansowe funkcjonariusze) którego zagrożone konto będzie mieć znaczący wpływ, należy uwzględnić tej grupy. 

Jednak jeśli organizacja nie ma udostępnionych urządzeń, uwierzytelnianie wieloskładnikowe może nie można dla tych kont awaryjnych dostępu. Jeśli konfigurujesz zasady dostępu warunkowego, aby wymagać [rejestracji usługi Multi-Factor Authentication dla każdego administratora](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) dla usługi Azure AD, a drugą podłączoną oprogramowania jako aplikacje usługi (SaaS), może być konieczne skonfigurowanie zasad wykluczenia, aby wykluczyć kont awaryjnych dostępu z tego wymagania.

### <a name="initial-configuration-with-approvals"></a>Wstępna konfiguracja z zatwierdzeniami

Innym rozwiązaniem jest konfigurowanie użytkowników jako uprawniających i osoby zatwierdzające aktywować *administratora globalnego* roli. Ta opcja będzie wymagać Twojej organizacji, aby mieć subskrypcji Azure AD Premium P2. Będzie to wymagać również opcję uwierzytelnianie wieloskładnikowe, która jest odpowiednia do udostępnienia między wiele osób i środowisko sieciowe. Te wymagania są ponieważ aktywacji *administratora globalnego* roli wymaga od użytkowników wcześniej wykonano uwierzytelnianie wieloskładnikowe. Aby uzyskać więcej informacji, zobacz [sposobu wymagać usługi Multi-Factor Authentication w usłudze Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa).

Nie zaleca się przy użyciu usługi Multi-Factor Authentication, skojarzoną z urządzeń osobistych do awaryjnego dostępu do konta. W nagłych rzeczywiste osoba, która potrzebuje dostępu do urządzenia zarejestrowane przez usługę Multi-Factor Authentication może nie być osobą, która ma urządzeń osobistych. 

Należy również rozważyć zagrożeń. Na przykład nieprzewidzianych okoliczności, takich jak klęski żywiołowej awaryjnym mogą wystąpić, przez który telefon komórkowy lub innych sieci może być niedostępna. Jest ważne upewnić się, że wszystkie zarejestrowane urządzenia są przechowywane w znane, bezpiecznej lokalizacji, która zawiera wiele sposób komunikowania się z usługą Azure AD.

## <a name="ongoing-monitoring"></a>Ciągłe monitorowanie

Monitor [rejestruje się w usłudze Azure AD i inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) żadnych logowania i inspekcji działania z kont dostępu do awaryjnego. Zwykle tych kont nie powinny być logowanie i powinien nie być wprowadzania zmian, więc korzystanie z nich jest prawdopodobnie nietypowych i wymagają dochodzenia zabezpieczeń.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>Sprawdzanie poprawności konta wyboru musi przypadać w regularnych odstępach czasu

Aby zweryfikować konto, wykonaj następujące kroki, co najmniej:
- Co 90 dni.
- Jeśli wprowadzono zmiany w personel działu informatycznego, takie jak zmiana zadania, wyjścia lub nowego zatrudnienia.
- Jeśli zmieniono subskrypcji usługi Azure AD w organizacji.

W celu przeszkolenia pracowników za pomocą kont dostępu awaryjnego, wykonaj następujące czynności:

* Upewnij się, że monitorowanie zabezpieczeń personelu wiedzą trwa sprawdzenie konta działania.
* Sprawdź, czy kont użytkowników w chmurze zalogowanie się i aktywować ich ról i że użytkownicy, którzy może być konieczne, wykonaj następujące kroki w sytuacji awaryjnej są uczone procesu.
* Upewnij się, że nie zarejestrowanych uwierzytelnianie wieloskładnikowe lub samoobsługowego resetowania hasła (SSPR) do urządzenia lub dane osobowe dowolnego użytkownika. 
* Jeśli konta są zarejestrowane w usłudze Multi-Factor Authentication na urządzeniu, do użycia podczas uaktywniania roli upewnij się, że urządzenie jest dostępny dla wszystkich administratorów, którzy może być konieczne w celu używania go w sytuacji awaryjnej. Sprawdź także, że urządzenie zostało zarejestrowane przez co najmniej dwa mechanizmy, które nie mają wspólnego trybu awaryjnego. Na przykład urządzenie może komunikować się z Internetem za pośrednictwem narzędzia obsługującego sieci bezprzewodowej i sieci dostawcy komórki.
* Zaktualizuj poświadczenia konta.

## <a name="next-steps"></a>Kolejne kroki
- [Dodaj użytkowników w chmurze](add-users-azure-active-directory.md) i [przypisać nowego użytkownika do roli administratora globalnego](active-directory-users-assign-role-azure-portal.md).
- [Załóż konto usługi Azure Active Directory Premium](active-directory-get-started-premium.md), jeśli masz utworzonego konta już.
- [Wymusić uwierzytelnianie wieloskładnikowe Azure dla poszczególnych użytkownicy przypisani jako Administratorzy](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).
- [Skonfiguruj stosowanie dodatkowych zabezpieczeń dla administratorów globalnych w usłudze Office 365](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560), jeśli używasz usługi Office 365.
- [Wykonanie przeglądu dostępu administratorów globalnych](active-directory-privileged-identity-management-how-to-start-security-review.md) i [przejście istniejących administratorów globalnych do bardziej szczegółowych ról administratora](active-directory-assign-admin-roles-azure-portal.md).


