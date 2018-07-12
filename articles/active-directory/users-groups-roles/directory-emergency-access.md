---
title: Zarządzanie kontami administracyjnymi z dostępem awaryjnego w usłudze Azure AD | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób użycia kont dostępu awaryjnego ułatwiające organizacjom ograniczenie dostępu uprzywilejowanego w istniejącym środowisku usługi Azure Active Directory.
services: active-directory
author: markwahl-msft
ms.author: billmath
ms.date: 12/13/2017
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.openlocfilehash: 4f3772abc1cdbd3b35b8b1f16e7a47c0f1a17783
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38595658"
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>Zarządzanie kontami administracyjnymi z dostępem awaryjnego w usłudze Azure AD 

Dla większości codziennych działań *administratora globalnego* prawa nie są wymagane przez użytkowników. Użytkownicy powinna nie można trwale przypisana do tej roli, ponieważ przypadkowo wykonują zadanie, które wymaga wyższych uprawnień nie powinny mieć. Gdy użytkownicy nie muszą pełnić rolę administratora globalnego, należy aktywować przypisania roli, przy użyciu usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM), na ich własnego konta lub alternatywnego konta administracyjnego.

Oprócz użytkowników biorąc praw dostępu administracyjnego samodzielnie, należy zapobiegać przypadkowo zablokowania dostępu do zarządzania dzierżawą usługi Azure AD, ponieważ nie można zarejestrować ani aktywować konto istniejących danego użytkownika jako Administrator. Można łagodzenia skutków przypadkowego braku dostępu administratora dzięki przechowywaniu co najmniej dwóch *kont dostępu awaryjnego* w dzierżawie.

Kont dostępu awaryjnego może pomóc organizacjom ograniczenie dostępu uprzywilejowanego w istniejącym środowisku usługi Azure Active Directory. Takie konta są wysoce uprzywilejowane, a nie są one przypisane do konkretnych osób. Kont dostępu awaryjnego są ograniczone do awaryjnego lub instrukcja "break szkła" scenariuszy sytuacje, gdy nie można użyć normalnego konta z uprawnieniami administracyjnymi. Organizacje, musisz utrzymywać na celu ograniczenie użycia konta dostępu awaryjnego tylko do tego czasu, w którym jest to konieczne.

Organizacja może być konieczne użycie konta dostępu awaryjnego w następujących sytuacjach:

 - Konta użytkowników są federacyjne i Federacji jest obecnie niedostępna z powodu przerwania połączenia sieciowego komórki lub awaria dostawcy tożsamości. Na przykład jeśli host dostawcy tożsamości w danym środowisku uległy awarii, użytkownicy mogą mieć nie można zalogować się przypadku usługi Azure AD przekierowuje do jego dostawcy tożsamości. 
 - Administratorzy są rejestrowane za pomocą usługi Azure Multi-Factor Authentication i ich poszczególne urządzenia są niedostępne. Użytkownicy mogą mieć nie może wykonać uwierzytelnianie wieloskładnikowe, aby aktywować rolę. Na przykład zaniku połączenia sieciowego komórki uniemożliwia ich z odpowiadanie na połączenia telefoniczne lub odbieranie wiadomości SMS, tylko dwa mechanizmów uwierzytelniania, które są zarejestrowane dla swojego urządzenia. 
 - Osoba mająca najbardziej aktualną globalny dostęp administracyjny opuścił organizację. Usługi Azure AD zapobiega ostatniego *administratora globalnego* Zmiana warstwy konta z usuwany, ale nie uniemożliwia konto przed usunięciem lub wyłączone w środowisku lokalnym. Nie można odzyskać na koncie organizacji może spowodować, że albo sytuacji.

## <a name="initial-configuration"></a>Wstępna konfiguracja

Utwórz co najmniej dwóch kont dostępu awaryjnego. Powinny być kontami tylko w chmurze, korzystających z \*. domeny onmicrosoft.com oraz że nie są Sfederowane lub zsynchronizowane ze środowiska lokalnego. 

Konta nie powinien być skojarzony z dowolnego użytkownika w organizacji. Organizacje wymagają upewnić się, że poświadczenia dla tych kont pozostają bezpieczne i znanych tylko dla osób, które są autoryzowane do korzystania z nich. 

> [!NOTE]
> Hasło do konta dla kont dostępu awaryjnego jest zwykle podzielić na dwie lub trzy części, zapisywane na osobne fragmenty dokument i przechowywane w bezpieczny i ognioodporną sejfy, znajdujących się w bezpieczny i oddzielnych lokalizacjach. 
>
> Upewnij się, że konta dostępu awaryjnego nie są połączone za pomocą dowolnego dostarczone przez pracownika telefony komórkowe, sprzętu tokenów, które są przesyłane przy użyciu poszczególnych pracowników lub inne poświadczenia danego pracownika. Ten środek ostrożności obejmuje wystąpień, w którym dany pracownik jest nieosiągalny, jeśli potrzebne są poświadczenia. 

### <a name="initial-configuration-with-permanent-assignments"></a>Konfiguracji początkowej trwałe przypisania

Jedną z opcji jest zapewnienie użytkownikom stałego członkowie *administratora globalnego* roli. Ta opcja będzie odpowiednia dla organizacji, które nie mają subskrypcji usługi Azure AD Premium P2.

Aby ograniczyć ryzyko ataku, wynikające z naruszonymi zabezpieczeniami hasła, usługi Azure AD, zaleca się, Wymagaj uwierzytelniania wieloskładnikowego dla wszystkich użytkowników indywidualnych. Ta grupa powinna zawierać administratorów i wszystkich innych (na przykład, dyrektorów finansowych) ze złamanymi zabezpieczeniami, którego konto może mieć znaczący wpływ. 

Jednak jeśli Twoja organizacja nie ma udostępnionych urządzeń, uwierzytelnianie wieloskładnikowe może nie być możliwe dla tych kont dostępu awaryjnego. Jeśli konfigurujesz zasady dostępu warunkowego, które wymagają [rejestracji uwierzytelniania wieloskładnikowego dla każdego administratora](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) dla usługi Azure AD i inne połączone oprogramowania jako usługi (SaaS), może być konieczne konfigurowanie zasad wykluczenia, aby wykluczyć kont dostępu awaryjnego z tego wymagania.

### <a name="initial-configuration-with-approvals"></a>Początkowa konfiguracja z zatwierdzeniami

Inną opcją jest od konfiguracji użytkowników oraz uprawnionych osób zatwierdzających, aby aktywować *administratora globalnego* roli. Ta opcja wymaga posiadania subskrypcji usługi Azure AD Premium P2 dla Twojej organizacji. Będzie to wymagać również opcję uwierzytelnianie wieloskładnikowe, które jest odpowiednie dla udostępnionych używanych przez wiele osób i środowisko sieciowe. Te wymagania są, ponieważ aktywacji *administratora globalnego* rola wymaga od użytkowników wcześniej wykonano uwierzytelnianie wieloskładnikowe. Aby uzyskać więcej informacji, zobacz [instrukcje ustawiania wymogu uwierzytelniania wieloskładnikowego w usłudze Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa).

Firma Microsoft nie zaleca się zastosowanie uwierzytelniania Multi-Factor Authentication, która jest skojarzona z urządzeń osobistych na potrzeby kont dostępu awaryjnego. W nagłych rzeczywiste osoby, która wymaga dostępu do urządzenia zarejestrowane usługi Multi-Factor Authentication może nie być osobą, która ma urządzeń osobistych. 

Należy również rozważyć zagrożeniach. Na przykład nieprzewidziane okoliczności, takich jak klęski żywiołowe awaryjnym mogą wystąpić, przez który telefon komórkowy lub innych sieci mogą być niedostępne. Należy upewnić się, że wszystkie zarejestrowane urządzenia są przechowywane w lokalizacji znanych, bezpiecznych, który ma wiele sposób komunikowania się z usługą Azure AD.

## <a name="ongoing-monitoring"></a>Trwającą monitorowania

Monitor [usługi Azure AD, logowania i inspekcji dzienników](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) dla dowolnego logowania i inspekcji aktywności z kont dostępu awaryjnego. Zazwyczaj te konta nie powinien być logowanie i powinna nie być wprowadzania zmian, więc korzystanie z nich jest prawdopodobnie nietypowe i będzie wymagać badania zabezpieczeń.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>Sprawdzanie poprawności sprawdzenie konta musi przypadać w regularnych odstępach czasu

Aby zweryfikować konto, wykonaj następujące kroki, co najmniej:
- Co 90 dni.
- Gdy dojdzie Ostatnia zmiana w zespole IT, takie jak zmiana zadania, wyjścia lub nowego zatrudnienia.
- Jeśli zmieniły się subskrypcji usługi Azure AD w organizacji.

To w opracowywaniu członków personelu, aby używać kont dostępu awaryjnego, wykonaj następujące czynności:

* Upewnij się, że monitorowanie zabezpieczeń personelu świadomość Trwa sprawdzanie konta działania.
* Zweryfikuj, że konta użytkowników w chmurze do logowania i aktywacji ich ról oraz wykwalifikowani osób, które może być konieczne wykonanie tych kroków w sytuacji awaryjnej procesu.
* Upewnij się, że ich nie dokonano rejestracji uwierzytelniania wieloskładnikowego lub samoobsługowego resetowania haseł (SSPR) na urządzeniu lub dane osobowe dowolnego użytkownika. 
* Konta są zarejestrowane dla usługi Multi-Factor Authentication na urządzeniu, do użytku podczas aktywacji roli upewnij się, że urządzenie jest dostępny dla wszystkich administratorów, którzy mogą potrzebować go używać w sytuacji awaryjnej. Sprawdź także, że urządzenie zostało zarejestrowane za pośrednictwem co najmniej dwa mechanizmy, które nie mają wspólnego trybu awaryjnego. Na przykład urządzenie może komunikować się z Internetem za pośrednictwem funkcji sieci bezprzewodowej i sieci dostawcy komórki.
* Aktualizowanie poświadczeń konta.

## <a name="next-steps"></a>Kolejne kroki
- [Dodawanie użytkownika oparte na chmurze](../fundamentals/add-users-azure-active-directory.md) i [przypisać nowego użytkownika do roli administratora globalnego](../fundamentals/active-directory-users-assign-role-azure-portal.md).
- [Zarejestruj się w usłudze Azure Active Directory — wersja Premium](../fundamentals/active-directory-get-started-premium.md), jeśli jeszcze nie zostało już utworzone konto.
- [Wymagaj uwierzytelniania wieloskładnikowego Azure dla poszczególnych użytkowników, przypisani jako Administratorzy](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).
- [Skonfiguruj dodatkową ochronę dla Administratorzy globalni w usłudze Office 365](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560), jeśli używasz usługi Office 365.
- [Wykonywanie przeglądu dostępu administratorów globalnych](../privileged-identity-management/pim-how-to-start-security-review.md) i [przeniesienie istniejących administratorów globalnych do bardziej konkretnych ról administratora](directory-assign-admin-roles.md).


