---
title: Usługa Azure Active Directory — często zadawane pytania | Dokumentacja firmy Microsoft
description: Dowiedz się, typowych pytań i odpowiedzi na pytania dotyczące platformy Azure i usługi Azure Active Directory, zarządzanie hasłami i uzyskiwaniem dostępu do aplikacji.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: lizross
ms.openlocfilehash: 5ff9d1cf76188ff38f59e8b74d4bfd380c8c88e1
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408714"
---
# <a name="azure-active-directory-faq"></a>Często zadawane pytania dotyczące usługi Azure Active Directory
Azure Active Directory (Azure AD) jest kompleksowym rozwiązaniem typu tożsamość jako usługa (IDaaS, Identity as a Service), które obejmuje wszystkie aspekty tożsamości, zarządzania dostępem i bezpieczeństwa.

Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Uzyskiwanie dostępu do platformy Azure i usługi Azure Active Directory
**Pytanie: dlaczego otrzymuję błąd „Nie znaleziono żadnych subskrypcji” podczas próby dostępu do usługi Azure AD w witrynie Azure Portal?**

**Odpowiedź:** aby uzyskać dostęp do witryny Azure Portal, każdy użytkownik musi mieć uprawnienia w ramach subskrypcji platformy Azure. Jeśli korzystasz z płatnej licencji usługi Office 365 lub Azure AD, przejdź do strony [https://aka.ms/accessAAD](https://aka.ms/accessAAD), aby użyć jednorazowej aktywacji. W przeciwnym razie należy aktywować bezpłatne [konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/) lub płatną subskrypcję.

Aby uzyskać więcej informacji, zobacz:

* [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- - -
**Pytanie: jaka jest relacja między usługami Azure AD, Office 365 i platformą Azure?**

**Odpowiedź:** usługa Azure AD zawiera typowe funkcje związane z tożsamością i dostępem do wszystkich usług sieci Web. Niezależnie od tego, czy używasz usługi Office 365, Microsoft Azure, Intune lub innych, już używasz usługi Azure AD w celu umożliwienia logowania i zarządzania dostępem do tych wszystkich usług.

Wszyscy użytkownicy skonfigurowani do używania usług sieci Web są zdefiniowani jako konta użytkowników w co najmniej jednym wystąpieniu usługi Azure AD. Możesz skonfigurować te konta dla bezpłatnych funkcji usługi Azure AD, np. dostępu do aplikacji w chmurze.

Usługi płatne Azure AD, takie jak Enterprise Mobility + Security, uzupełniają inne usługi sieci Web, np. Office 365 i Microsoft Azure, zapewniając kompleksowe rozwiązania z zakresu skalowalnego zarządzania dla przedsiębiorstw i bezpieczeństwa.

- - -

**Pytanie: jakie są różnice między właściciela i administratora globalnego?**

**Odp.:** , osoby, która zarejestruje się w subskrypcji platformy Azure jest domyślnie przypisywany roli właściciela dla zasobów platformy Azure. Właściciel można użyć konta Microsoft lub konta służbowego z katalogu, w którym jest skojarzona subskrypcja platformy Azure, za pomocą.  Ta rola jest autoryzowana do zarządzania usługami w witrynie Azure Portal.

Jeśli inni użytkownicy potrzebują logować się i uzyskać dostęp do usług za pomocą tej samej subskrypcji, możesz przypisać im odpowiednie [wbudowana rola](../../role-based-access-control/built-in-roles.md). Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md).

Domyślnie osoba, która zarejestruje się w subskrypcji platformy Azure przypisano rolę administratora globalnego dla katalogu. Administrator globalny ma dostęp do wszystkich funkcji katalogu usługi Azure AD. Usługa Azure AD ma inny zestaw ról administratora na potrzeby zarządzania katalogiem i funkcjami dotyczącymi tożsamości. Ci Administratorzy mają dostęp do różnych funkcji w witrynie Azure portal. Rola administratora Określa, co może zrobić, jak utworzyć lub edytować użytkowników, przypisywać role administracyjne innym osobom, resetować hasła użytkowników, zarządzać licencjami użytkowników lub zarządzać domenami.  Aby uzyskać dodatkowe informacje na temat administratorów usługi Azure AD i ich ról, zobacz [przypisać użytkownika do ról administratora w usłudze Azure Active Directory](active-directory-users-assign-role-azure-portal.md) i [przypisywanie ról administratorów w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

Ponadto usługi płatne Azure AD, takie jak Enterprise Mobility + Security, uzupełniają inne usługi sieci Web, np. Office 365 i Microsoft Azure, zapewniając kompleksowe rozwiązania z zakresu skalowalnego zarządzania dla przedsiębiorstw i bezpieczeństwa.

- - -
**Pytanie: czy istnieje raport, który pokazuje, kiedy wygaśnie moja licencja użytkownika usługi Azure AD?**

**Odpowiedź:** Nie.  Taki raport nie jest obecnie dostępny.

- - -

## <a name="get-started-with-hybrid-azure-ad"></a>Wprowadzenie do hybrydowej usługi Azure AD


**Pytanie: jak opuścić dzierżawę, gdy dodano mnie do niej jako współpracownika?**

**Odpowiedź:** w przypadku dodania do dzierżawy innej organizacji jako współpracownika możesz przełączać się między dzierżawami za pomocą „przełącznika dzierżawy” w prawym górnym rogu.  Obecnie nie ma możliwości opuszczenia organizacji zapraszającej. Firma Microsoft pracuje nad dodaniem tej funkcji.  Do czasu udostępnienia tej funkcji rozwiązaniem może być poproszenie organizacji zapraszającej o usunięcie z dzierżawy.
- - -
**Pytanie: jak połączyć katalog lokalny z usługą Azure AD?**

**Odpowiedź:** możesz połączyć katalog lokalny z usługą Azure AD przy użyciu narzędzia Azure AD Connect.

Aby uzyskać więcej informacji, zobacz [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

- - -
**Pytanie: jak skonfigurować logowanie jednokrotne (SSO) między katalogiem lokalnym i aplikacjami w chmurze?**

**Odpowiedź:** wystarczy skonfigurować logowanie jednokrotne (SSO) między katalogiem lokalnym i usługą Azure AD. Jeśli tylko uzyskujesz dostęp do aplikacji w chmurze poprzez usługę Azure AD, usługa automatycznie wymusza od użytkowników odpowiednie uwierzytelnianie przy użyciu ich lokalnych poświadczeń.

Implementowanie logowania jednokrotnego z pozycji lokalnej można z łatwością przeprowadzić przy użyciu rozwiązań federacyjnych, np. usług Active Directory Federation Services, lub przez skonfigurowanie synchronizacji skrótów haseł. Możesz z łatwością wdrożyć obie opcje, korzystając z kreatora konfiguracji programu Azure AD Connect.

Aby uzyskać więcej informacji, zobacz [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

- - -
**Pytanie: czy usługa Azure AD zawiera samoobsługowy portal dla użytkowników w organizacji?**

**Odpowiedź:** tak, usługa Azure AD zapewnia [Panel dostępu usługi Azure AD](http://myapps.microsoft.com) do samoobsługi użytkowników i dostępu do aplikacji. Jeśli korzystasz z usługi Office 365, możesz znaleźć wiele tych samych funkcji w portalu usługi Office 365.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

- - -
**Pytanie: czy usługa Azure AD pomaga w zarządzaniu infrastrukturą lokalną?**

**Odpowiedź:** tak. Usługa Azure AD Premium zawiera program Azure AD Connect Health. Program Azure AD Connect Health pomaga monitorować i zdobywać informacje o lokalnej infrastrukturze do obsługi tożsamości oraz usługach synchronizacji.  

Aby uzyskać więcej informacji, zobacz [Monitorowanie lokalnej infrastruktury do obsługi tożsamości i usług synchronizacji w chmurze](../hybrid/whatis-hybrid-identity-health.md).  

- - -
## <a name="password-management"></a>Zarządzanie hasłami
**Pytanie: czy można użyć funkcji zapisywania zwrotnego haseł usługi Azure AD bez synchronizacji haseł? (Czy w tym scenariuszu można użyć funkcji samoobsługowego resetowania haseł (SSPR) usługi Azure AD z zapisywaniem zwrotnym haseł bez przechowywania haseł w chmurze?)**

**Odpowiedź:** nie musisz synchronizować haseł usługi Active Directory z usługą Azure AD, aby korzystać z funkcji zapisywania zwrotnego. W środowisku federacyjnym logowanie jednokrotne usługi Azure AD opiera się na katalogu lokalnym w celu uwierzytelniania użytkownika. Ten scenariusz nie wymaga śledzenia lokalnego hasła w usłudze Azure AD.

- - -
**Pytanie: jak długo trwa zwrotne zapisanie hasła w lokalnym wystąpieniu usługi Active Directory?**

**Odpowiedź:** zapisywanie zwrotne haseł działa w czasie rzeczywistym.

Więcej informacji można znaleźć w temacie [Wprowadzenie do zarządzania hasłami](../authentication/quickstart-sspr.md)

- - -
**Pytane: czy mogę użyć funkcji zapisywania zwrotnego haseł wobec haseł zarządzanych przez administratora?**

**Odpowiedź:** tak, jeśli włączysz funkcję zapisywania zwrotnego haseł, operacje na hasłach wykonywane przez administratora będą zwrotnie zapisywane w środowisku lokalnym.  

Aby uzyskać więcej odpowiedzi na pytania związane z hasłami, zobacz [Często zadawane pytania dotyczące zarządzania hasłami](../authentication/active-directory-passwords-faq.md).
- - -
**Pytanie: co zrobić, jeśli chcę zmienić hasło usługi Office 365/Azure AD, a nie pamiętam istniejącego hasła?**

**Odpowiedź:** w takiej sytuacji istnieje kilka opcji.  Użyj funkcji samoobsługowego resetowania haseł (SSPR), jeśli jest dostępna.  To, czy funkcja samoobsługowego resetowania haseł działa, zależy od tego, jak została skonfigurowana.  Aby uzyskać więcej informacji, zobacz [Jak działa portal resetowania haseł](../authentication/howto-sspr-deployment.md).

Jeśli jesteś użytkownikiem usługi Office 365, Twój administrator może zresetować hasło, wykonując kroki opisane w artykule [Resetowanie haseł użytkowników](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

W przypadku kont usługi Azure AD administratorzy mogą zresetować hasło w jeden z następujących sposobów:

- [Resetowanie kont w witrynie Azure Portal](active-directory-users-reset-password-azure-portal.md)
- [Korzystanie z programu PowerShell](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


- - -
## <a name="security"></a>Bezpieczeństwo
**Pytanie: czy konta są blokowane po określonej liczbie nieudanych prób, czy jest stosowana bardziej zaawansowana strategia?**

Korzystamy z bardziej zaawansowanej strategii blokowania kont.  Jest ona oparta na adresie IP żądania i wprowadzonym haśle. Czas trwania blokady wydłuża się też w zależności od stopnia prawdopodobieństwa ataku.  

**Pytanie: niektóre (typowe) hasła są odrzucane i jest wyświetlany komunikat z informacją o tym, że hasło zostało użyte zbyt wiele razy. Czy dotyczy to haseł używanych w bieżącej usłudze Active Directory?**

Dotyczy to typowych haseł występujących globalnie, takich jak różne odmiany ciągów „Hasło” i „123456”.

**Pytanie: czy żądanie logowania z podejrzanych źródeł (botnety, punkt końcowy sieci Tor) zostanie zablokowane w dzierżawie B2C, czy wymaga to dzierżawy w warstwie Podstawowa lub Premium?**

Oferujemy bramę, która filtruje żądania i zapewnia ochronę przed botnetami. Jest ona stosowana we wszystkich dzierżawach B2C.

## <a name="application-access"></a>Dostęp do aplikacji

**Pytanie: gdzie mogę znaleźć listę aplikacji, które są wstępnie zintegrowane z usługą Azure AD i jej funkcjami?**

**Odpowiedź:** usługa Azure AD ma ponad 2600 wstępnie zintegrowanych aplikacji od firmy Microsoft, dostawców usług aplikacji i partnerów. Wszystkie wstępnie zintegrowane aplikacje obsługują logowanie jednokrotne. Logowanie jednokrotne umożliwia uzyskiwanie dostępu do aplikacji przy użyciu poświadczeń organizacji. Niektóre aplikacje obsługują również zautomatyzowaną aprowizację i cofanie aprowizacji.

Aby uzyskać pełną listę wstępnie zintegrowanych aplikacji, zobacz [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

- - -
**Pytanie: co w przypadku, jeśli aplikacja, której potrzebuję, nie znajduje się w witrynie Azure AD Marketplace?**

**Odpowiedź:** przy użyciu usługi Azure AD Premium możesz dodać i skonfigurować dowolną aplikację. W zależności od możliwości aplikacji i preferencji możesz skonfigurować logowanie jednokrotne i automatyczną aprowizację.  

Aby uzyskać więcej informacji, zobacz:

* [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Włączanie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji przy użyciu SCIM](../manage-apps/use-scim-to-provision-users-and-groups.md)

- - -
**Pytanie: jak użytkownicy logują się do aplikacji przy użyciu usługi Azure AD?**

**Odpowiedź:** usługa Azure AD oferuje użytkownikom kilka możliwości wyświetlania aplikacji i uzyskiwania do nich dostępu, np.:

* Panel dostępu usługi Azure AD
* Program uruchamiający aplikacje usługi Office 365
* Bezpośrednie logowanie do aplikacji federacyjnych
* Linki bezpośrednie do federacyjnych, opartych na hasłach lub istniejących aplikacjach

Aby uzyskać więcej informacji, zobacz [Wdrażanie zintegrowanych aplikacji usługi Azure AD dla użytkowników](../manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users).

- - -
**Pytanie: jakie są różne sposoby włączania uwierzytelniania i logowania jednokrotnego do aplikacji przez usługę Azure AD?**

**Odpowiedź:** usługa Azure AD obsługuje wiele standardowych protokołów uwierzytelniania i autoryzacji, takich jak SAML 2.0, OpenID Connect, OAuth 2.0 i WS-Federation. Usługa Azure AD obsługuje również archiwizowanie haseł i automatyczne logowanie do aplikacji, które obsługują wyłącznie uwierzytelnianie oparte na formularzach.  

Aby uzyskać więcej informacji, zobacz:

* [Scenariusze uwierzytelniania dla usługi Azure AD](../develop/authentication-scenarios.md)
* [Protokoły uwierzytelniania usługi Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Jak działa logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work)

- - -
**Pytanie: czy mogę dodać aplikacje uruchamiane lokalnie?**

**Odpowiedź:** serwer proxy aplikacji usługi Azure AD zapewnia prosty i bezpieczny dostęp do wybranych lokalnych aplikacji internetowych. Dostęp do tych aplikacji można uzyskiwać w taki sam sposób jak w przypadku aplikacji typu oprogramowanie jako usługa (SaaS) w usłudze Azure AD. Nie ma potrzeby stosowania sieci VPN ani zmiany infrastruktury sieciowej.  

Aby uzyskać więcej informacji, zobacz [How to provide secure remote access to on-premises applications](../manage-apps/application-proxy.md) (Jak zapewnić bezpieczny zdalny dostęp do aplikacji lokalnych).

- - -
**Pytanie: jak wymagać uwierzytelniania wieloskładnikowego dla użytkowników uzyskujących dostęp do określonej aplikacji?**

**Odpowiedź:** przy użyciu dostępu warunkowego usługi Azure AD możesz przypisać unikatowe zasady dostępu dla każdej aplikacji. W zasadach możesz wymagać korzystania z uwierzytelniania wieloskładnikowego w każdym przypadku lub wtedy, gdy użytkownicy nie są połączeni z siecią lokalną.  

Aby uzyskać więcej informacji, zobacz [Zabezpieczanie dostępu do usługi Office 365 i innych aplikacji podłączonych do usługi Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

- - -
**Pytanie: co to jest automatyczna aprowizacja użytkowników dla aplikacji SaaS?**

**Odpowiedź:** usługa Azure AD umożliwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w wielu popularnych aplikacjach SaaS w chmurze.

Aby uzyskać więcej informacji, zobacz [Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](../manage-apps/user-provisioning.md) (Automatyzacja aprowizacji i cofania aprowizacji użytkowników dla aplikacji SaaS przy użyciu usługi Azure Active Directory).

- - -
**Pytanie: czy mogę skonfigurować bezpieczne połączenie LDAP z usługą Azure AD?**

**Odpowiedź:** nie. Usługa Azure AD nie obsługuje protokołu LDAP. Jednak za pomocą usług domenowych Azure AD można skonfigurować protokołu secure LDAP.
