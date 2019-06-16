---
title: Zarządzać kontami administratorów dostępu awaryjnego — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób użycia kont dostępu awaryjnego, aby zapobiec przypadkowo zablokowania dostępu do dzierżawy usługi Azure Active Directory (Azure AD).
services: active-directory
author: markwahl-msft
ms.author: curtand
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42de060d81539030ef1970e01e753383662e924f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083907"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD

Należy zapobiegać przypadkowemu zablokowania dostępu do dzierżawy usługi Azure Active Directory (Azure AD) ponieważ nie można zalogować lub aktywować istniejące poszczególne konta z uprawnieniami administratora. Pozwala ograniczyć wpływ przypadkowego braku dostępu administracyjnego przez utworzenie co najmniej dwóch *kont dostępu awaryjnego* w dzierżawie.

Kont dostępu awaryjnego są wysoce uprzywilejowane, a nie są one przypisane do konkretnych osób. Kont dostępu awaryjnego są ograniczone do awaryjnego lub scenariuszach "break szkła", gdy nie można użyć normalnego konta z uprawnieniami administracyjnymi. Organizacje, musisz utrzymywać na celu ograniczenie użycia konta dostępu awaryjnego do czasu, gdy jest to absolutnie konieczne.

Ten artykuł zawiera wskazówki dotyczące zarządzania kont dostępu awaryjnego w usłudze Azure AD.

## <a name="when-would-you-use-an-emergency-access-account"></a>Kiedy należy używać kont dostępu awaryjnego?

Organizacja może być konieczne użycie konta dostępu awaryjnego w następujących sytuacjach:

- Konta użytkowników są federacyjne i Federacji jest obecnie niedostępna z powodu przerwania połączenia sieciowego komórki lub awaria dostawcy tożsamości. Na przykład jeśli host dostawcy tożsamości w danym środowisku uległy awarii, użytkownicy mogą mieć nie można zalogować się przypadku usługi Azure AD przekierowuje do jego dostawcy tożsamości.
- Administratorzy są rejestrowane za pomocą usługi Azure Multi-Factor Authentication, a ich poszczególne urządzenia są niedostępne lub usługa jest niedostępna. Użytkownicy mogą mieć nie może wykonać uwierzytelnianie wieloskładnikowe, aby aktywować rolę. Na przykład zaniku połączenia sieciowego komórki uniemożliwia ich z odpowiadanie na połączenia telefoniczne lub odbieranie wiadomości SMS, tylko dwa mechanizmów uwierzytelniania, które są zarejestrowane dla swojego urządzenia.
- Osoba mająca najbardziej aktualną dostępu administratora globalnego opuścił organizację. Usługa Azure AD zapobiega usunięciu ostatniego konta administratora globalnego, ale nie uniemożliwia konto przed usunięciem lub wyłączone w środowisku lokalnym. Nie można odzyskać na koncie organizacji może spowodować, że albo sytuacji.
- Nieprzewidziane okoliczności, takich jak klęski żywiołowe awaryjnego, podczas których telefon komórkowy lub innych sieci mogą być niedostępne. 

## <a name="create-two-cloud-based-emergency-access-accounts"></a>Utwórz dwa konta dostępu awaryjnego oparte na chmurze

Utwórz co najmniej dwóch kont dostępu awaryjnego. Te konta powinny znajdować się tylko na chmurze kont, korzystających z \*. domeny onmicrosoft.com oraz że nie są Sfederowane lub zsynchronizowane ze środowiska lokalnego.

Podczas konfigurowania tych kont, muszą być spełnione następujące wymagania:

- Kont dostępu awaryjnego nie powinna być skojarzona z żadnym użytkownikiem poszczególnych w organizacji. Upewnij się, że konta nie są połączone za pomocą dowolnego dostarczone przez pracownika telefony komórkowe, sprzętu tokenów, które są przesyłane przy użyciu poszczególnych pracowników lub inne poświadczenia danego pracownika. Ten środek ostrożności obejmuje wystąpień, w którym dany pracownik jest nieosiągalny, jeśli potrzebne są poświadczenia. Należy upewnić się, że wszystkie zarejestrowane urządzenia są przechowywane w lokalizacji znanych, bezpiecznych, który ma wiele sposób komunikowania się z usługą Azure AD.
- Mechanizm uwierzytelniania, używane jako konto dostępu awaryjnego powinny się różnić od używanego przez usługi innych kont administracyjnych, łącznie z innych kont dostępu awaryjnego.  Na przykład jeśli logowanie administratora normalne za pomocą lokalnej usługi MFA, uwierzytelnianie wieloskładnikowe Azure będzie innego mechanizmu.  Jednak jeśli usługi Azure MFA jest Twoje podstawowego uwierzytelniania dla kont z uprawnieniami administracyjnymi, rozważ jest różne podejście w tym przypadku np. przy użyciu dostępu warunkowego przy użyciu dostawcy usługi MFA innych firm.
- Urządzenie lub poświadczeń nie wygaśnie ani się mieścić w zakresie automatycznego oczyszczania ze względu na brak użytkowania.  
- Należy ustawić przypisania roli administratora globalnego stałe dla Twoich kont dostępu awaryjnego. 


### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Wyklucz co najmniej jedno konto z telefonu, na podstawie uwierzytelniania wieloskładnikowego

Aby ograniczyć ryzyko ataku, wynikające z naruszonymi zabezpieczeniami hasła, usługi Azure AD, zaleca się, Wymagaj uwierzytelniania wieloskładnikowego dla wszystkich użytkowników indywidualnych. Ta grupa zawiera administratorów i wszystkich innych (na przykład, dyrektorów finansowych) ze złamanymi zabezpieczeniami, którego konto może mieć znaczący wpływ.

Jednak co najmniej jedno z kont dostępu awaryjnego nie powinny mieć ten sam mechanizm uwierzytelniania wieloskładnikowego, jak w przypadku innych kont bez awaryjnego. Obejmuje to rozwiązania innych firm usługi Multi-Factor authentication. Jeśli masz zasady dostępu warunkowego, które wymagają [uwierzytelnianie wieloskładnikowe dla każdego administratora](../authentication/howto-mfa-userstates.md) dla usługi Azure AD i inne oprogramowanie połączone jako usługi (SaaS), należy wyłączyć kont dostępu awaryjnego z tego wymagania i zamiast tego skonfiguruj innego mechanizmu. Ponadto należy upewnij się, że konta nie mają zasady uwierzytelniania wieloskładnikowego dla poszczególnych użytkowników.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Wyklucz co najmniej jedno konto z zasad dostępu warunkowego

W sytuacji awaryjnej nie ma zasad, aby uniemożliwić dostęp w celu rozwiązania problemu. Co najmniej jedno konto dostępu awaryjnego powinny być wykluczone z wszystkie zasady dostępu warunkowego. Po włączeniu [bazowymi zasadami](../conditional-access/baseline-protection.md), należy wyłączyć konta dostępu awaryjnego.

## <a name="additional-guidance-for-hybrid-customers"></a>Dodatkowe wytyczne dla klientów hybrydowych

Jest dodatkową opcję dla organizacji, które używają usług domenowych AD i AD FS lub podobne dostawcy tożsamości Federację z usługą Azure AD, aby skonfigurować konto dostępu awaryjnego, w których oświadczenia usługi MFA może być dostarczone przez tego dostawcę tożsamości.  Na przykład kont dostępu awaryjnego może być objęta para certyfikat i klucz taki przechowywane na karcie inteligentnej.  Gdy ten użytkownik jest uwierzytelniany usługą AD, usług AD FS można podać oświadczenia do usługi Azure AD, wskazującą, czy użytkownik ma spełnione wymagania uwierzytelniania MFA.  Nawet w przypadku tej metody organizacje muszą mieć kont dostępu awaryjnego oparte na chmurze w przypadku federacji nie może zostać ustanowione. 

## <a name="store-devices-and-credentials-in-a-safe-location"></a>Store urządzenia i poświadczenia w bezpiecznym miejscu

Organizacje wymagają upewnić się, że poświadczenia dla kont dostępu awaryjnego pozostają bezpieczne i znanych tylko dla osób, które są autoryzowane do korzystania z nich. Niektórzy klienci Użyj karty inteligentnej, a inne korzystają z hasła. Hasło do konta dostępu awaryjnego jest zwykle podzielić na dwie lub trzy części, zapisanych na osobne fragmenty dokument i przechowywane w bezpieczny i ognioodporną sejfy, znajdujących się w bezpieczny, oddzielne lokalizacje.

Jeśli używasz hasła, upewnij się, że konta mają silnych haseł, które nie wygasa hasło. W idealnym przypadku hasła powinna być co najmniej 16 znaków długo i losowo generowany.


## <a name="monitor-sign-in-and-audit-logs"></a>Monitorowanie logowania i dzienników inspekcji

Monitor [usługi Azure AD, logowania i inspekcji dzienników](../reports-monitoring/concept-sign-ins.md) dla dowolnego logowania i inspekcji aktywności z kont dostępu awaryjnego. Zazwyczaj te konta nie powinien być logowanie i powinna nie być wprowadzania zmian, więc korzystanie z nich jest prawdopodobnie nietypowe i będzie wymagać badania zabezpieczeń.

## <a name="validate-accounts-at-regular-intervals"></a>Sprawdzanie poprawności kont w regularnych odstępach czasu

To w opracowywaniu członków personelu, aby używać kont dostępu awaryjnego i zweryfikować kont dostępu awaryjnego, wykonaj następujące czynności minimalne w regularnych odstępach czasu:

- Upewnij się, że monitorowanie zabezpieczeń personelu świadomość Trwa sprawdzanie konta działania.
- Upewnij się, że proces szkła podziału awaryjnego można korzystać z tych kont udokumentowane i aktualne.
- Upewnij się, administratorów i specjaliści ds. bezpieczeństwa, które może być konieczne wykonanie tych kroków w sytuacji awaryjnej wykwalifikowani procesu.
- Zaktualizuj poświadczenia konta, w szczególności hasła, dla Twoich kont dostępu awaryjnego, a następnie sprawdź, czy kont dostępu awaryjnego można logowania i wykonywać zadania administracyjne.
- Upewnij się, że użytkowników, którzy nie zarejestrowali uwierzytelniania wieloskładnikowego lub samoobsługowego resetowania haseł (SSPR) na urządzeniu dowolnego użytkownika lub dane osobowe. 
- Konta są zarejestrowane dla usługi Multi-Factor Authentication na urządzeniu, do użytku podczas logowania lub roli aktywacji, upewnij się, że urządzenie jest dostępny dla wszystkich administratorów, którzy mogą potrzebować go używać w sytuacji awaryjnej. Sprawdź także, czy urządzenie może komunikować się za pośrednictwem co najmniej dwóch ścieżek sieciowych, które nie mają wspólnego trybu awaryjnego. Na przykład urządzenie może komunikować się z Internetem za pośrednictwem funkcji sieci bezprzewodowej i sieci dostawcy komórki.

Te kroki należy wykonać w regularnych odstępach czasu i zmian klucza:

- Co najmniej co 90 dni
- Gdy dojdzie Ostatnia zmiana w zespole IT, takie jak zmiana zadania, wyjścia lub nowego zatrudnienia
- Jeśli zmieniono subskrypcji usługi Azure AD w organizacji

## <a name="next-steps"></a>Kolejne kroki

- [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD](directory-admin-roles-secure.md)
- [Dodaj użytkowników za pomocą usługi Azure AD](../fundamentals/add-users-azure-active-directory.md) i [przypisać nowego użytkownika do roli administratora globalnego](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Załóż konto usługi Azure AD Premium](../fundamentals/active-directory-get-started-premium.md), jeżeli nie zarejestrujesz się już
- [Jak, które wymuszają weryfikację dwuetapową dla użytkownika](../authentication/howto-mfa-userstates.md)
- [Skonfiguruj dodatkową ochronę dla Administratorzy globalni w usłudze Office 365](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts), jeśli używasz usługi Office 365
- [Uruchamianie przeglądu dostępu administratorów globalnych](../privileged-identity-management/pim-how-to-start-security-review.md) i [przeniesienie istniejących administratorów globalnych do bardziej konkretnych ról administratora](directory-assign-admin-roles.md)
