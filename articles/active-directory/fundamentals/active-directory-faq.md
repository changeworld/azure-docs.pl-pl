---
title: Często zadawane pytania (FAQ) — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Typowe pytania i odpowiedzi na pytania dotyczące platformy Azure i usługi Azure Active Directory, zarządzanie hasłami i uzyskiwaniem dostępu do aplikacji.
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
ms.openlocfilehash: 4c1ee5e849d8004f828a2d92d728ad7925fc05c4
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693949"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Często zadawane pytania dotyczące usługi Azure Active Directory
Azure Active Directory (Azure AD) jest kompleksowym rozwiązaniem typu tożsamość jako usługa (IDaaS, Identity as a Service), które obejmuje wszystkie aspekty tożsamości, zarządzania dostępem i bezpieczeństwa.

Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Uzyskiwanie dostępu do platformy Azure i usługi Azure Active Directory
**Pyt.: Dlaczego otrzymuję "nie znaleziono żadnych subskrypcji" podczas próby dostępu do usługi Azure AD w Azure Portal?**

**Odp.:** Aby uzyskać dostęp do Azure Portal, każdy użytkownik musi mieć uprawnienia w ramach subskrypcji platformy Azure. Jeśli korzystasz z płatnej licencji usługi Office 365 lub Azure AD, przejdź do strony [https://aka.ms/accessAAD](https://aka.ms/accessAAD), aby użyć jednorazowej aktywacji. W przeciwnym razie należy aktywować bezpłatne [konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/) lub płatną subskrypcję.

Aby uzyskać więcej informacji, zobacz:

* [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

---
**Pyt.: Jaka jest relacja między usługą Azure AD, pakietem Office 365 i platformą Azure?**

**Odp.:** Usługa Azure AD zapewnia typowe funkcje tożsamości i dostępu do wszystkich usług sieci Web. Niezależnie od tego, czy używasz usługi Office 365, Microsoft Azure, Intune lub innych, już używasz usługi Azure AD w celu umożliwienia logowania i zarządzania dostępem do tych wszystkich usług.

Wszyscy użytkownicy skonfigurowani do używania usług sieci Web są zdefiniowani jako konta użytkowników w co najmniej jednym wystąpieniu usługi Azure AD. Możesz skonfigurować te konta dla bezpłatnych funkcji usługi Azure AD, np. dostępu do aplikacji w chmurze.

Usługi płatne Azure AD, takie jak Enterprise Mobility + Security, uzupełniają inne usługi sieci Web, np. Office 365 i Microsoft Azure, zapewniając kompleksowe rozwiązania z zakresu skalowalnego zarządzania dla przedsiębiorstw i bezpieczeństwa.

---

**Pyt.:  Jakie są różnice między właścicielem a administratorem globalnym?**

**Odp.:** Domyślnie osoba, która zarejestruje się w celu uzyskania subskrypcji platformy Azure, ma przypisaną rolę właściciela dla zasobów platformy Azure. Właściciel można użyć konta Microsoft lub konta służbowego z katalogu, w którym jest skojarzona subskrypcja platformy Azure, za pomocą.  Ta rola jest autoryzowana do zarządzania usługami w witrynie Azure Portal.

Jeśli inni użytkownicy potrzebują logować się i uzyskać dostęp do usług za pomocą tej samej subskrypcji, możesz przypisać im odpowiednie [wbudowana rola](../../role-based-access-control/built-in-roles.md). Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md).

Domyślnie osoba, która zarejestruje się w subskrypcji platformy Azure przypisano rolę administratora globalnego dla katalogu. Administrator globalny ma dostęp do wszystkich funkcji katalogu usługi Azure AD. Usługa Azure AD ma inny zestaw ról administratora na potrzeby zarządzania katalogiem i funkcjami dotyczącymi tożsamości. Ci Administratorzy mają dostęp do różnych funkcji w witrynie Azure portal. Rola administratora Określa, co może zrobić, jak utworzyć lub edytować użytkowników, przypisywać role administracyjne innym osobom, resetować hasła użytkowników, zarządzać licencjami użytkowników lub zarządzać domenami.  Aby uzyskać dodatkowe informacje na temat administratorów usługi Azure AD i ich ról, zobacz [przypisać użytkownika do ról administratora w usłudze Azure Active Directory](active-directory-users-assign-role-azure-portal.md) i [przypisywanie ról administratorów w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

Ponadto usługi płatne Azure AD, takie jak Enterprise Mobility + Security, uzupełniają inne usługi sieci Web, np. Office 365 i Microsoft Azure, zapewniając kompleksowe rozwiązania z zakresu skalowalnego zarządzania dla przedsiębiorstw i bezpieczeństwa.

---
**Pyt.: Czy istnieje raport, który pokazuje, kiedy licencje użytkownika usługi Azure AD wygasną?**

**Odp.:** Nie.  Taki raport nie jest obecnie dostępny.

---

## <a name="get-started-with-hybrid-azure-ad"></a>Wprowadzenie do hybrydowej usługi Azure AD


**Pyt.: Jak mogę opuścić dzierżawę po dodaniu jako współpracownika?**

**Odp.:** Po dodaniu do dzierżawy innej organizacji jako współpracownika możesz użyć opcji "przełącznik dzierżawy" w prawym górnym rogu, aby przełączyć się między dzierżawcami.  Obecnie nie ma możliwości opuszczenia organizacji zapraszającej. Firma Microsoft pracuje nad dodaniem tej funkcji.  Do czasu udostępnienia tej funkcji rozwiązaniem może być poproszenie organizacji zapraszającej o usunięcie z dzierżawy.

---
**Pyt.: Jak połączyć katalog lokalny z usługą Azure AD?**

**Odp.:** Możesz połączyć katalog lokalny z usługą Azure AD przy użyciu Azure AD Connect.

Aby uzyskać więcej informacji, zobacz [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

---
**Pyt.: Jak mogę skonfigurować Logowanie jednokrotne między katalogiem lokalnym i aplikacjami w chmurze?**

**Odp.:** Wystarczy skonfigurować Logowanie jednokrotne (SSO) między katalogiem lokalnym i usługą Azure AD. Jeśli tylko uzyskujesz dostęp do aplikacji w chmurze poprzez usługę Azure AD, usługa automatycznie wymusza od użytkowników odpowiednie uwierzytelnianie przy użyciu ich lokalnych poświadczeń.

Implementowanie logowania jednokrotnego z pozycji lokalnej można z łatwością przeprowadzić przy użyciu rozwiązań federacyjnych, np. usług Active Directory Federation Services, lub przez skonfigurowanie synchronizacji skrótów haseł. Możesz z łatwością wdrożyć obie opcje, korzystając z kreatora konfiguracji programu Azure AD Connect.

Aby uzyskać więcej informacji, zobacz [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

---
**Pyt.: Czy usługa Azure AD udostępnia Portal samoobsługowy dla użytkowników w mojej organizacji?**

**Odp.:** Tak. usługa Azure AD udostępnia [panel dostępu usługi Azure AD](https://myapps.microsoft.com) , który umożliwia samoobsługowe i dostęp do aplikacji użytkownika. Jeśli jesteś klientem z pakietem Office 365, możesz znaleźć wiele tych samych funkcji w [portalu pakietu Office 365](https://portal.office.com).

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

---
**Pyt.: Czy usługa Azure AD pomaga w zarządzaniu infrastrukturą lokalną?**

**Odp.:** Tak. Usługa Azure AD Premium zawiera program Azure AD Connect Health. Program Azure AD Connect Health pomaga monitorować i zdobywać informacje o lokalnej infrastrukturze do obsługi tożsamości oraz usługach synchronizacji.  

Aby uzyskać więcej informacji, zobacz [Monitorowanie lokalnej infrastruktury do obsługi tożsamości i usług synchronizacji w chmurze](../hybrid/whatis-hybrid-identity-health.md).  

---
## <a name="password-management"></a>Zarządzanie hasłami
**Pyt.: Czy mogę użyć funkcji zapisywania zwrotnego haseł usługi Azure AD bez synchronizacji haseł? (Czy w tym scenariuszu można użyć funkcji samoobsługowego resetowania haseł (SSPR) usługi Azure AD z zapisywaniem zwrotnym haseł bez przechowywania haseł w chmurze?)**

**Odp.:** Nie musisz synchronizować Active Directory haseł do usługi Azure AD, aby umożliwić zapisywanie zwrotne. W środowisku federacyjnym logowanie jednokrotne usługi Azure AD opiera się na katalogu lokalnym w celu uwierzytelniania użytkownika. Ten scenariusz nie wymaga śledzenia lokalnego hasła w usłudze Azure AD.

---
**Pyt.: Jak długo trwa zapisywanie hasła z powrotem do Active Directory lokalnego?**

**Odp.:** Zapisywanie zwrotne haseł działa w czasie rzeczywistym.

Więcej informacji można znaleźć w temacie [Wprowadzenie do zarządzania hasłami](../authentication/quickstart-sspr.md)

---
**Pyt.: Czy mogę użyć funkcji zapisywania zwrotnego haseł z hasłami zarządzanymi przez administratora?**

**Odp.:** Tak, jeśli włączono funkcję zapisywania zwrotnego haseł, operacje na hasłach wykonywane przez administratora są zapisywane z powrotem w środowisku lokalnym.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questionsauthenticationactive-directory-passwords-faqmd"></a>Aby uzyskać więcej odpowiedzi na pytania związane z hasłami, zobacz [Często zadawane pytania dotyczące zarządzania hasłami](../authentication/active-directory-passwords-faq.md).
---
**Pyt.:  Co mogę zrobić, jeśli nie mogę zapamiętać mojego istniejącego hasła usługi Office 365/Azure AD podczas próby zmiany hasła?**

**Odp.:** W przypadku tego typu sytuacji istnieje kilka opcji.  Użyj funkcji samoobsługowego resetowania haseł (SSPR), jeśli jest dostępna.  To, czy funkcja samoobsługowego resetowania haseł działa, zależy od tego, jak została skonfigurowana.  Aby uzyskać więcej informacji, zobacz [Jak działa portal resetowania haseł](../authentication/howto-sspr-deployment.md).

Jeśli jesteś użytkownikiem usługi Office 365, Twój administrator może zresetować hasło, wykonując kroki opisane w artykule [Resetowanie haseł użytkowników](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

W przypadku kont usługi Azure AD administratorzy mogą zresetować hasło w jeden z następujących sposobów:

- [Resetowanie kont w witrynie Azure Portal](active-directory-users-reset-password-azure-portal.md)
- [Korzystanie z programu PowerShell](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


---
## <a name="security"></a>Bezpieczeństwo
**Pyt.: Czy konta są blokowane po określonej liczbie nieudanych prób lub czy jest używana bardziej zaawansowanej strategii?**

Korzystamy z bardziej zaawansowanej strategii blokowania kont.  Jest ona oparta na adresie IP żądania i wprowadzonym haśle. Czas trwania blokady wydłuża się też w zależności od stopnia prawdopodobieństwa ataku.  

**Pyt.:  Niektóre (typowe) hasła są odrzucane z komunikatami "to hasło jest używane do wielu razy", czy dotyczy to haseł używanych w bieżącej usłudze Active Directory?**

Dotyczy to typowych haseł występujących globalnie, takich jak różne odmiany ciągów „Hasło” i „123456”.

**Pyt.: Czy żądanie logowania ze źródeł podejrzanych (botnetami, punkt końcowy tor) będzie blokowane w dzierżawie B2C lub czy wymaga dzierżawy wersji Basic lub Premium?**

Oferujemy bramę, która filtruje żądania i zapewnia ochronę przed botnetami. Jest ona stosowana we wszystkich dzierżawach B2C.

## <a name="application-access"></a>Dostęp do aplikacji

**Pyt.: Gdzie mogę znaleźć listę aplikacji, które są wstępnie zintegrowane z usługą Azure AD i ich możliwościami?**

**Odp.:** Usługa Azure AD ma ponad 2 600 wstępnie zintegrowanych aplikacji od firmy Microsoft, dostawców usług aplikacji i partnerów. Wszystkie wstępnie zintegrowane aplikacje obsługują logowanie jednokrotne. Logowanie jednokrotne umożliwia uzyskiwanie dostępu do aplikacji przy użyciu poświadczeń organizacji. Niektóre aplikacje obsługują również zautomatyzowaną aprowizację i cofanie aprowizacji.

Aby uzyskać pełną listę wstępnie zintegrowanych aplikacji, zobacz [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

---
**Pyt.: Co zrobić, jeśli aplikacja, której potrzebuję, nie znajduje się w witrynie Azure AD Marketplace?**

**Odp.:** Za pomocą Azure AD — wersja Premium możesz dodać i skonfigurować dowolną aplikację. W zależności od możliwości aplikacji i preferencji możesz skonfigurować logowanie jednokrotne i automatyczną aprowizację.  

Aby uzyskać więcej informacji, zobacz:

* [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Włączanie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji przy użyciu SCIM](../manage-apps/use-scim-to-provision-users-and-groups.md)

---
**Pyt.: Jak użytkownicy logują się do aplikacji za pomocą usługi Azure AD?**

**Odp.:** Usługa Azure AD zapewnia użytkownikom kilka sposobów wyświetlania aplikacji i uzyskiwania do nich dostępu, takich jak:

* Panel dostępu usługi Azure AD
* Program uruchamiający aplikacje usługi Office 365
* Bezpośrednie logowanie do aplikacji federacyjnych
* Linki bezpośrednie do federacyjnych, opartych na hasłach lub istniejących aplikacjach

Aby uzyskać więcej informacji, zobacz [interfejsu użytkownika końcowego aplikacji](../manage-apps/end-user-experiences.md).

---
**Pyt.: Jakie są różne sposoby uwierzytelniania i logowania jednokrotnego do aplikacji w usłudze Azure AD?**

**Odp.:** Usługa Azure AD obsługuje wiele standardowych protokołów uwierzytelniania i autoryzacji, takich jak SAML 2,0, OpenID Connect Connect, OAuth 2,0 i WS-Federation. Usługa Azure AD obsługuje również archiwizowanie haseł i automatyczne logowanie do aplikacji, które obsługują wyłącznie uwierzytelnianie oparte na formularzach.  

Aby uzyskać więcej informacji, zobacz:

* [Scenariusze uwierzytelniania dla usługi Azure AD](../develop/authentication-scenarios.md)
* [Protokoły uwierzytelniania usługi Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Logowanie jednokrotne dla aplikacji w usłudze Azure AD](../manage-apps/what-is-single-sign-on.md)

---
**Pyt.: Czy mogę dodać aplikacje uruchamiane lokalnie?**

**Odp.:** Usługa Azure serwer proxy aplikacji usługi Azure AD zapewnia łatwy i bezpieczny dostęp do wybranych lokalnych aplikacji sieci Web. Dostęp do tych aplikacji można uzyskiwać w taki sam sposób jak w przypadku aplikacji typu oprogramowanie jako usługa (SaaS) w usłudze Azure AD. Nie ma potrzeby stosowania sieci VPN ani zmiany infrastruktury sieciowej.  

Aby uzyskać więcej informacji, zobacz [How to provide secure remote access to on-premises applications](../manage-apps/application-proxy.md) (Jak zapewnić bezpieczny zdalny dostęp do aplikacji lokalnych).

---
**Pyt.: Jak mogę wymagać uwierzytelniania wieloskładnikowego dla użytkowników, którzy uzyskują dostęp do określonej aplikacji?**

**Odp.:** Dostęp warunkowy do usługi Azure AD umożliwia przypisywanie unikatowych zasad dostępu dla każdej aplikacji. W zasadach możesz wymagać korzystania z uwierzytelniania wieloskładnikowego w każdym przypadku lub wtedy, gdy użytkownicy nie są połączeni z siecią lokalną.  

Aby uzyskać więcej informacji, zobacz [Zabezpieczanie dostępu do usługi Office 365 i innych aplikacji podłączonych do usługi Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

---
**Pyt.: Co to jest zautomatyzowana funkcja aprowizacji użytkowników dla aplikacji SaaS?**

**Odp.:** Usługa Azure AD umożliwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w wielu popularnych aplikacjach SaaS w chmurze.

Aby uzyskać więcej informacji, zobacz [Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](../manage-apps/user-provisioning.md) (Automatyzacja aprowizacji i cofania aprowizacji użytkowników dla aplikacji SaaS przy użyciu usługi Azure Active Directory).

---
**Pyt.:  Czy mogę skonfigurować bezpieczne połączenie LDAP z usługą Azure AD?**

**Odp.:**  Nie. Usługa Azure AD nie obsługuje protokołu Lightweight Directory Access Protocol (LDAP) ani Secure LDAP bezpośrednio. Możliwe jest jednak włączenie wystąpienia Azure AD Domain Services (Azure AD DS) w dzierżawie usługi Azure AD z odpowiednio skonfigurowanymi grupami zabezpieczeń sieci za pośrednictwem sieci platformy Azure, aby zapewnić łączność z protokołem LDAP. Aby uzyskać więcej informacji, zobacz https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.
