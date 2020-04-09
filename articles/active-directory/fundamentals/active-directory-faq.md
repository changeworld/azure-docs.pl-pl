---
title: Często zadawane pytania ( CZĘSTO ZADAWANE PYTANIA) — Usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Typowe pytania i odpowiedzi dotyczące platformy Azure i usługi Azure Active Directory, zarządzania hasłami i dostępu do aplikacji.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4a961e601949689db89f8819f0a1fe1c5a7b3a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875796"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Często zadawane pytania dotyczące usługi Azure Active Directory
Azure Active Directory (Azure AD) jest kompleksowym rozwiązaniem typu tożsamość jako usługa (IDaaS, Identity as a Service), które obejmuje wszystkie aspekty tożsamości, zarządzania dostępem i bezpieczeństwa.

Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Uzyskiwanie dostępu do platformy Azure i usługi Azure Active Directory
**Pyt.: Dlaczego podczas próby uzyskania dostępu do usługi Azure AD w witrynie Azure portal otrzymuję "Nie znaleziono żadnych subskrypcji"?**

**Odpowiedź:** aby uzyskać dostęp do witryny Azure Portal, każdy użytkownik musi mieć uprawnienia w ramach subskrypcji platformy Azure. Jeśli nie masz płatnej subskrypcji usługi Office 365 lub usługi Azure AD, musisz aktywować bezpłatne [konto platformy Azure](https://azure.microsoft.com/free/
) lub płatną subskrypcję.

Aby uzyskać więcej informacji, zobacz:

* [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

---
**Pyt.: Jaka jest relacja między usługą Azure AD, Office 365 i platformą Azure?**

**Odpowiedź:** usługa Azure AD zawiera typowe funkcje związane z tożsamością i dostępem do wszystkich usług sieci Web. Niezależnie od tego, czy używasz usługi Office 365, Microsoft Azure, Intune lub innych, już używasz usługi Azure AD w celu umożliwienia logowania i zarządzania dostępem do tych wszystkich usług.

Wszyscy użytkownicy skonfigurowani do używania usług sieci Web są zdefiniowani jako konta użytkowników w co najmniej jednym wystąpieniu usługi Azure AD. Możesz skonfigurować te konta dla bezpłatnych funkcji usługi Azure AD, np. dostępu do aplikacji w chmurze.

Usługi płatne Azure AD, takie jak Enterprise Mobility + Security, uzupełniają inne usługi sieci Web, np. Office 365 i Microsoft Azure, zapewniając kompleksowe rozwiązania z zakresu skalowalnego zarządzania dla przedsiębiorstw i bezpieczeństwa.

---

**P: Jakie są różnice między właścicielem a administratorem globalnym?**

**Odp.:** Domyślnie osobie, która zarejestruje się w ramach subskrypcji platformy Azure, jest przypisana rola właściciela zasobów platformy Azure. Właściciel może używać konta Microsoft lub konta służbowego z katalogu, z którego jest skojarzona subskrypcja platformy Azure.  Ta rola jest autoryzowana do zarządzania usługami w witrynie Azure Portal.

Jeśli inni muszą się zalogować i uzyskać dostęp do usług przy użyciu tej samej subskrypcji, można przypisać im odpowiednią [wbudowaną rolę](../../role-based-access-control/built-in-roles.md). Aby uzyskać dodatkowe informacje, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC i portalu Azure](../../role-based-access-control/role-assignments-portal.md).

Domyślnie osobie, która zarejestruje się w ramach subskrypcji platformy Azure, jest przypisana rola administratora globalnego dla katalogu. Administrator globalny ma dostęp do wszystkich funkcji katalogu usługi Azure AD. Usługa Azure AD ma inny zestaw ról administratora do zarządzania funkcjami katalogu i tożsamości. Administratorzy ci będą mieli dostęp do różnych funkcji w witrynie Azure portal. Rola administratora określa, co mogą zrobić, na przykład tworzyć lub edytować użytkowników, przypisywać role administracyjne innym osobom, resetować hasła użytkowników, zarządzać licencjami użytkowników lub zarządzać domenami.  Aby uzyskać dodatkowe informacje na temat administratorów katalogów usługi Azure AD i ich ról, zobacz [Przypisywanie ról administratora w usłudze Azure Active Directory](active-directory-users-assign-role-azure-portal.md) i [przypisywanie ról administratora w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

Ponadto usługi płatne Azure AD, takie jak Enterprise Mobility + Security, uzupełniają inne usługi sieci Web, np. Office 365 i Microsoft Azure, zapewniając kompleksowe rozwiązania z zakresu skalowalnego zarządzania dla przedsiębiorstw i bezpieczeństwa.

---
**Pytanie: czy istnieje raport, który pokazuje, kiedy wygaśnie moja licencja użytkownika usługi Azure AD?**

**Odpowiedź:** Nie.  Taki raport nie jest obecnie dostępny.

---

## <a name="get-started-with-hybrid-azure-ad"></a>Wprowadzenie do hybrydowej usługi Azure AD


**Pytanie: jak opuścić dzierżawę, gdy dodano mnie do niej jako współpracownika?**

**Odpowiedź:** w przypadku dodania do dzierżawy innej organizacji jako współpracownika możesz przełączać się między dzierżawami za pomocą „przełącznika dzierżawy” w prawym górnym rogu.  Obecnie nie ma możliwości opuszczenia organizacji zapraszającej. Firma Microsoft pracuje nad dodaniem tej funkcji.  Do czasu udostępnienia tej funkcji rozwiązaniem może być poproszenie organizacji zapraszającej o usunięcie z dzierżawy.

---
**Pytanie: jak połączyć katalog lokalny z usługą Azure AD?**

**Odpowiedź:** możesz połączyć katalog lokalny z usługą Azure AD przy użyciu narzędzia Azure AD Connect.

Aby uzyskać więcej informacji, zobacz [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

---
**Pytanie: jak skonfigurować logowanie jednokrotne (SSO) między katalogiem lokalnym i aplikacjami w chmurze?**

**Odpowiedź:** wystarczy skonfigurować logowanie jednokrotne (SSO) między katalogiem lokalnym i usługą Azure AD. Jeśli tylko uzyskujesz dostęp do aplikacji w chmurze poprzez usługę Azure AD, usługa automatycznie wymusza od użytkowników odpowiednie uwierzytelnianie przy użyciu ich lokalnych poświadczeń.

Implementowanie logowania jednokrotnego z lokalnego rozwiązania można łatwo osiągnąć za pomocą rozwiązań federacyjnych, takich jak Usługi federacyjne Active Directory (AD FS), lub przez skonfigurowanie synchronizacji skrótów haseł. Obie opcje można łatwo wdrożyć za pomocą kreatora konfiguracji usługi Azure AD Connect.

Aby uzyskać więcej informacji, zobacz [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

---
**Pytanie: czy usługa Azure AD zawiera samoobsługowy portal dla użytkowników w organizacji?**

**Odpowiedź:** tak, usługa Azure AD zapewnia [Panel dostępu usługi Azure AD](https://myapps.microsoft.com) do samoobsługi użytkowników i dostępu do aplikacji. Jeśli jesteś klientem usługi Office 365, możesz znaleźć wiele takich samych możliwości w [portalu usługi Office 365](https://portal.office.com).

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

---
**Pytanie: czy usługa Azure AD pomaga w zarządzaniu infrastrukturą lokalną?**

**Odpowiedź:** tak. Usługa Azure AD Premium zawiera program Azure AD Connect Health. Program Azure AD Connect Health pomaga monitorować i zdobywać informacje o lokalnej infrastrukturze do obsługi tożsamości oraz usługach synchronizacji.  

Aby uzyskać więcej informacji, zobacz [Monitorowanie lokalnej infrastruktury do obsługi tożsamości i usług synchronizacji w chmurze](../hybrid/whatis-hybrid-identity-health.md).  

---
## <a name="password-management"></a>Zarządzanie hasłami
**Pyt.: Czy mogę użyć hasła usługi Azure AD do tyłu bez synchronizacji hasła? (W tym scenariuszu, czy można użyć samoobsługowego resetowania haseł usługi Azure AD (SSPR) z odpisem hasła i nie przechowywać haseł w chmurze?)**

**Odpowiedź:** nie musisz synchronizować haseł usługi Active Directory z usługą Azure AD, aby korzystać z funkcji zapisywania zwrotnego. W środowisku federacyjnym logowanie jednokrotne usługi Azure AD opiera się na katalogu lokalnym w celu uwierzytelniania użytkownika. Ten scenariusz nie wymaga śledzenia lokalnego hasła w usłudze Azure AD.

---
**Pytanie: jak długo trwa zwrotne zapisanie hasła w lokalnym wystąpieniu usługi Active Directory?**

**Odpowiedź:** zapisywanie zwrotne haseł działa w czasie rzeczywistym.

Więcej informacji można znaleźć w temacie [Wprowadzenie do zarządzania hasłami](../authentication/quickstart-sspr.md)

---
**Pytane: czy mogę użyć funkcji zapisywania zwrotnego haseł wobec haseł zarządzanych przez administratora?**

**Odpowiedź:** tak, jeśli włączysz funkcję zapisywania zwrotnego haseł, operacje na hasłach wykonywane przez administratora będą zwrotnie zapisywane w środowisku lokalnym.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questions"></a>Aby uzyskać więcej odpowiedzi na pytania związane z hasłami, zobacz [Często zadawane pytania dotyczące zarządzania hasłami](../authentication/active-directory-passwords-faq.md).
---
**Pytanie: co zrobić, jeśli chcę zmienić hasło usługi Office 365/Azure AD, a nie pamiętam istniejącego hasła?**

**Odpowiedź:** w takiej sytuacji istnieje kilka opcji.  Użyj funkcji samoobsługowego resetowania haseł (SSPR), jeśli jest dostępna.  To, czy funkcja samoobsługowego resetowania haseł działa, zależy od tego, jak została skonfigurowana.  Aby uzyskać więcej informacji, zobacz [Jak działa portal resetowania haseł](../authentication/howto-sspr-deployment.md).

Jeśli jesteś użytkownikiem usługi Office 365, Twój administrator może zresetować hasło, wykonując kroki opisane w artykule [Resetowanie haseł użytkowników](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

W przypadku kont usługi Azure AD administratorzy mogą zresetować hasło w jeden z następujących sposobów:

- [Resetowanie kont w witrynie Azure Portal](active-directory-users-reset-password-azure-portal.md)
- [Korzystanie z programu PowerShell](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


---
## <a name="security"></a>Zabezpieczenia
**Pytanie: czy konta są blokowane po określonej liczbie nieudanych prób, czy jest stosowana bardziej zaawansowana strategia?**

Korzystamy z bardziej zaawansowanej strategii blokowania kont.  Jest ona oparta na adresie IP żądania i wprowadzonym haśle. Czas trwania blokady wydłuża się też w zależności od stopnia prawdopodobieństwa ataku.  

**Pyt.: Niektóre (wspólne) hasła są odrzucane z wiadomościami "to hasło było używane wiele razy", czy odnosi się to do haseł używanych w bieżącym usłudze Active Directory?**

Odnosi się to do haseł, które są powszechnie spotykane na całym świecie, takich jak wszelkie warianty "Hasło" i "123456".

**Pytanie: czy żądanie logowania z podejrzanych źródeł (botnety, punkt końcowy sieci Tor) zostanie zablokowane w dzierżawie B2C, czy wymaga to dzierżawy w warstwie Podstawowa lub Premium?**

Oferujemy bramę, która filtruje żądania i zapewnia ochronę przed botnetami. Jest ona stosowana we wszystkich dzierżawach B2C.

## <a name="application-access"></a>Dostęp do aplikacji

**Pytanie: gdzie mogę znaleźć listę aplikacji, które są wstępnie zintegrowane z usługą Azure AD i jej funkcjami?**

**Odpowiedź:** usługa Azure AD ma ponad 2600 wstępnie zintegrowanych aplikacji od firmy Microsoft, dostawców usług aplikacji i partnerów. Wszystkie wstępnie zintegrowane aplikacje obsługują logowanie jednokrotne. Logowanie jednokrotne umożliwia uzyskiwanie dostępu do aplikacji przy użyciu poświadczeń organizacji. Niektóre aplikacje obsługują również zautomatyzowaną aprowizację i cofanie aprowizacji.

Aby uzyskać pełną listę wstępnie zintegrowanych aplikacji, zobacz [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

---
**Pytanie: co w przypadku, jeśli aplikacja, której potrzebuję, nie znajduje się w witrynie Azure AD Marketplace?**

**Odpowiedź:** przy użyciu usługi Azure AD Premium możesz dodać i skonfigurować dowolną aplikację. W zależności od możliwości aplikacji i preferencji można skonfigurować funkcję SSO i automatyczne inicjowanie obsługi administracyjnej.  

Aby uzyskać więcej informacji, zobacz:

* [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Włączanie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji przy użyciu SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)

---
**Pytanie: jak użytkownicy logują się do aplikacji przy użyciu usługi Azure AD?**

**Odpowiedź:** usługa Azure AD oferuje użytkownikom kilka możliwości wyświetlania aplikacji i uzyskiwania do nich dostępu, np.:

* Panel dostępu usługi Azure AD
* Program uruchamiający aplikacje usługi Office 365
* Bezpośrednie logowanie do aplikacji federacyjnych
* Linki bezpośrednie do federacyjnych, opartych na hasłach lub istniejących aplikacjach

Aby uzyskać więcej informacji, zobacz [Środowisko użytkowników końcowych dla aplikacji](../manage-apps/end-user-experiences.md).

---
**Pytanie: jakie są różne sposoby włączania uwierzytelniania i logowania jednokrotnego do aplikacji przez usługę Azure AD?**

**Odpowiedź:** usługa Azure AD obsługuje wiele standardowych protokołów uwierzytelniania i autoryzacji, takich jak SAML 2.0, OpenID Connect, OAuth 2.0 i WS-Federation. Usługa Azure AD obsługuje również archiwizowanie haseł i automatyczne logowanie do aplikacji, które obsługują wyłącznie uwierzytelnianie oparte na formularzach.  

Aby uzyskać więcej informacji, zobacz:

* [Scenariusze uwierzytelniania dla usługi Azure AD](../develop/authentication-scenarios.md)
* [Protokoły uwierzytelniania usługi Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Logowanie jednokrotne dla aplikacji w usłudze Azure AD](../manage-apps/what-is-single-sign-on.md)

---
**Pyt.: Czy mogę dodać aplikacje, które uruchamiam lokalnie?**

**Odpowiedź:** serwer proxy aplikacji usługi Azure AD zapewnia prosty i bezpieczny dostęp do wybranych lokalnych aplikacji internetowych. Dostęp do tych aplikacji można uzyskiwać w taki sam sposób jak w przypadku aplikacji typu oprogramowanie jako usługa (SaaS) w usłudze Azure AD. Nie ma potrzeby stosowania sieci VPN ani zmiany infrastruktury sieciowej.  

Aby uzyskać więcej informacji, zobacz [How to provide secure remote access to on-premises applications](../manage-apps/application-proxy.md) (Jak zapewnić bezpieczny zdalny dostęp do aplikacji lokalnych).

---
**Pytanie: jak wymagać uwierzytelniania wieloskładnikowego dla użytkowników uzyskujących dostęp do określonej aplikacji?**

**Odp.:** Za pomocą usługi Azure AD Dostęp warunkowy, można przypisać unikatowe zasady dostępu dla każdej aplikacji. W zasadach możesz wymagać korzystania z uwierzytelniania wieloskładnikowego w każdym przypadku lub wtedy, gdy użytkownicy nie są połączeni z siecią lokalną.  

Aby uzyskać więcej informacji, zobacz [Zabezpieczanie dostępu do usługi Office 365 i innych aplikacji podłączonych do usługi Azure Active Directory](../conditional-access/overview.md).

---
**Pyt.: Co to jest automatyczne inicjowanie obsługi administracyjnej dla aplikacji SaaS?**

**Odpowiedź:** usługa Azure AD umożliwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w wielu popularnych aplikacjach SaaS w chmurze.

Aby uzyskać więcej informacji, zobacz [Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](../app-provisioning/user-provisioning.md) (Automatyzacja aprowizacji i cofania aprowizacji użytkowników dla aplikacji SaaS przy użyciu usługi Azure Active Directory).

---
**Pytanie: czy mogę skonfigurować bezpieczne połączenie LDAP z usługą Azure AD?**

**Odp.:**  №. Usługa Azure AD nie obsługuje bezpośrednio protokołu LDAP (Lightweight Directory Access Protocol) ani secure LDAP. Jednak jest możliwe, aby włączyć usługi azure ad usługi domenowe (Usługi Azure AD DS) wystąpienie w dzierżawie usługi Azure AD z prawidłowo skonfigurowanych grup zabezpieczeń sieci za pośrednictwem usługi Azure Networking do osiągnięcia łączności LDAP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie bezpiecznego protokołu LDAP dla domeny zarządzanej usług domenowych usługi active directory platformy Azure](../../active-directory-domain-services/tutorial-configure-ldaps.md)
