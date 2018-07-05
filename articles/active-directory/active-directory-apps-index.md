---
title: Artykuł indeksu do zarządzania aplikacjami w usłudze Azure Active Directory | Microsoft Azure
description: Dowiedz się, jak dostosować datę wygaśnięcia certyfikatów Federacji i odnawianie certyfikatów, które wkrótce wygasną.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 76430fa5652e98cba86c41433f376e27dbb1639a
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446864"
---
# <a name="article-index-for-application-management-in-azure-active-directory"></a>Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory
Ta strona zawiera pełną listę wszystkich dokumentów, zapisywane o różnych funkcjach związane z aplikacją w usłudze Azure Active Directory (Azure AD).

Krótkie wprowadzenie do każdego obszaru najważniejszych funkcji, a także wskazówki w artykułach, które można odczytać, w zależności od tego, jakich informacji szukasz nie istnieje.

## <a name="overview-articles"></a>Artykuły — omówienie
Poniższe artykuły są dobre punkt wyjścia dla osób chcących po prostu krótki opis funkcji zarządzania aplikacjami usługi Azure AD.

| Artykuł przewodnik |  |
|:---:| --- |
| Wprowadzenie do problemów z zarządzaniem aplikacji, które rozwiązuje usługa Azure AD |[Zarządzanie aplikacjami za pomocą usługi Azure Active Directory (AD)](manage-apps/what-is-application-management.md) |
| Omówienie różnych funkcji w usłudze Azure AD powiązany z włączaniem logowanie jednokrotne, określające, kto ma dostęp do aplikacji i jak użytkownikom uruchamianie aplikacji |[Dostęp do aplikacji i logowanie jednokrotne w usłudze Azure Active Directory](manage-apps/what-is-single-sign-on.md) |
| Przyjrzeć się różne etapy podczas integrowania aplikacji do usługi Azure AD |[Integrowanie usługi Azure Active Directory z aplikacjami](manage-apps/plan-an-application-integration.md)<br /><br />[Włączanie logowania jednokrotnego do aplikacji SaaS](manage-apps/configure-single-sign-on-portal.md)<br /><br />[Zarządzanie dostępem do aplikacji](manage-apps/what-is-access-management.md) |
| Techniczne omówienie, jak aplikacje są reprezentowane w usłudze Azure AD |[Jak i dlaczego aplikacje są dodawane do usługi Azure AD](active-directory-how-applications-are-added.md) |

## <a name="troubleshooting-articles"></a>Artykuły dotyczące rozwiązywania problemów
W tej sekcji zapewnia szybki dostęp do odpowiednich przewodniki dotyczące rozwiązywania problemów. Więcej informacji na temat każdego obszaru funkcji można znaleźć w pozostałej części tej strony.

| Obszar funkcji |  |
|:---:| --- |
| Federacyjne logowanie jednokrotne |[Rozwiązywanie problemów z opartej na SAML logowania jednokrotnego](active-directory-saml-debugging.md) |
| Na podstawie hasła logowania jednokrotnego |[Rozwiązywanie problemów z rozszerzenia Panelu dostępu do programu Internet Explorer](active-directory-saas-ie-troubleshooting.md) |
| Serwer proxy aplikacji |[Przewodnik rozwiązywania problemów dla serwera Proxy aplikacji](manage-apps/application-proxy-troubleshoot.md) |
| Logowanie jednokrotne między lokalnej usługi AD i Azure AD |[Rozwiązywanie problemów z synchronizacją skrótów haseł](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md#troubleshoot-password-hash-synchronization)<br /><br />[Rozwiązywanie problemów z zapisywaniem zwrotnym haseł](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Dynamiczne członkostwo w grupach |[Rozwiązywanie problemów z członkostwa w grupach dynamicznych](users-groups-roles/groups-troubleshooting.md) |

## <a name="single-sign-on-sso"></a>Logowanie jednokrotne
### <a name="federated-single-sign-on-sign-into-many-apps-using-one-identity"></a>Federacyjne logowanie jednokrotne: Logowania do wielu aplikacji przy użyciu jednej tożsamości
Logowanie jednokrotne umożliwia użytkownikom dostęp do różnych aplikacji i usług przy użyciu tylko jeden zestaw poświadczeń. Federacja znajduje się jedną z metod za pomocą których można włączyć logowanie jednokrotne. Gdy użytkownicy próbują zalogować się do aplikacji federacyjnych, będzie będzie uzyskać przekierowanie do swojej organizacji oficjalne logowania strony renderowane przez usługę Azure Active Directory, a następnie nastąpi przekierowanie do aplikacji po pomyślnym uwierzytelnieniu.

| Artykuł przewodnik |  |
|:---:| --- |
| Wprowadzenie do Federacji i inne typy logowania jednokrotnego |[Logowanie jednokrotne z usługą Azure AD](manage-apps/what-is-single-sign-on.md) |
| Tysiące aplikacji SaaS, które są wstępnie zintegrowane z usługą Azure AD za pomocą uproszczonego czynności konfiguracyjnych rejestracji jednokrotnej |[Rozpoczęcie korzystania z galerii aplikacji usługi Azure AD](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[Pełną listę wstępnie zintegrowanych aplikacji, które obsługują Federacji](saas-apps/tutorial-list.md)<br /><br />[Jak dodać aplikację do galerii aplikacji usługi Azure AD](active-directory-app-gallery-listing.md) |
| Więcej niż 150 samouczki aplikacji dotyczące sposobu konfigurowania logowania jednokrotnego dla aplikacji takich jak [Salesforce](saas-apps/salesforce-tutorial.md), [ServiceNow](saas-apps/servicenow-tutorial.md), [Google Apps](saas-apps/google-apps-tutorial.md), [Workday](saas-apps/workday-tutorial.md)i wiele więcej |[Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](saas-apps/tutorial-list.md) |
| Jak ręcznie skonfigurować i dostosować jednej konfiguracji logowania jednokrotnego |[Jak można skonfigurować federacyjne logowanie jednokrotne do aplikacji, które nie znajdują się w galerii aplikacji usługi Azure Active Directory](application-config-sso-how-to-configure-federated-sso-non-gallery.md)<br /><br />[Dostosowywanie oświadczeń wystawionych w tokenie SAML wstępnie zintegrowanych aplikacji](active-directory-saml-claims-customization.md) |
| Przewodnik rozwiązywania problemów dla aplikacji federacyjnych, które używają protokołu SAML |[Rozwiązywanie problemów z opartej na SAML logowania jednokrotnego](active-directory-saml-debugging.md) |
| Jak skonfigurować Data wygaśnięcia certyfikatu aplikacji i odnawianie certyfikatów |[Zarządzanie certyfikatami dla federacyjnego logowania jednokrotnego w usłudze Azure Active Directory](manage-apps/manage-certificates-for-federated-single-sign-on.md) |

Federacyjne logowanie jednokrotne jest dostępna dla wszystkich wersji programu Azure AD dla maksymalnie 10 aplikacji na użytkownika. [Usługa Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) obsługuje aplikacje bez ograniczeń. Jeśli Twoja organizacja ma [usługi Azure AD podstawowa](https://azure.microsoft.com/pricing/details/active-directory/) lub [usługi Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), a następnie możesz [używanie grup do udzielania dostępu do aplikacji federacyjnych](#managing-access-to-applications).

### <a name="password-based-single-sign-on-account-sharing-and-sso-for-non-federated-apps"></a>Oparte na hasłach logowanie jednokrotne: udostępnianie kont i logowanie Jednokrotne do aplikacji inne niż federacyjne
Aby włączyć logowanie jednokrotne do aplikacji, które nie obsługują Federacji, usługa Azure AD oferuje funkcje zarządzania hasło, które można bezpiecznie przechowywanie haseł do aplikacji SaaS i automatyczne logowanie użytkowników do tych aplikacji. Łatwo można rozpowszechnić poświadczenia dla nowo utworzonych kont i udostępniać konta zespołu wiele osób. Użytkownicy nie jest konieczna o poświadczenia konta, które mają one dostęp do.

| Artykuł przewodnik |  |
|:---:| --- |
| Wprowadzenie do sposobu opartego na hasłach działa usługa rejestracji Jednokrotnej i krótki opis techniczny |[Oparte na hasłach logowanie jednokrotne z usługą Azure AD](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on) |
| Podsumowanie scenariuszy związanych z udostępnianiem konta i jak te problemy są rozwiązane przez usługę Azure AD |[Udostępnianie kont w usłudze Azure AD](active-directory-sharing-accounts.md) |
| Automatyczna zmiana hasła w przypadku niektórych aplikacji w regularnych odstępach. |[Automatyczne przenoszenie haseł (wersja zapoznawcza)](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Wdrażanie i rozwiązywanie problemów z przewodniki dotyczące rozszerzenie zarządzania haseł usługi Azure AD w wersji programu Internet Explorer |[Jak wdrożyć rozszerzenia Panelu dostępu do programu Internet Explorer przy użyciu zasad grupy](active-directory-saas-ie-group-policy.md)<br /><br />[Rozwiązywanie problemów z rozszerzenia Panelu dostępu do programu Internet Explorer](active-directory-saas-ie-troubleshooting.md) |

Oparte na hasłach logowanie jednokrotne jest dostępna dla wszystkich wersji programu Azure AD dla maksymalnie 10 aplikacji na użytkownika. [Usługa Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) obsługuje aplikacje bez ograniczeń. Jeśli Twoja organizacja ma [usługi Azure AD podstawowa](https://azure.microsoft.com/pricing/details/active-directory/) lub [usługi Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), a następnie możesz [używanie grup do udzielania dostępu do aplikacji](#managing-access-to-applications). Przenoszenie haseł automatyczne jest [usługi Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkcji.

### <a name="app-proxy-single-sign-on-and-remote-access-to-on-premises-applications"></a>Serwer Proxy aplikacji: Jednokrotne logowanie jednokrotne i dostęp zdalny do aplikacji lokalnych
W przypadku aplikacji w sieci prywatnej, muszą być dostępne dla użytkowników i urządzeń, sieci i spoza niej można użyć serwera Proxy aplikacji usługi Azure AD, aby umożliwić bezpieczny, zdalny dostęp do tych aplikacji.

| Artykuł przewodnik |  |
|:---:| --- |
| Omówienie serwera Proxy aplikacji usługi Azure AD i jak to działa |[Zapewnianie bezpiecznego dostępu zdalnego do aplikacji lokalnych](manage-apps/application-proxy.md) |
| Samouczki dotyczące sposobu konfigurowania serwera Proxy aplikacji i jak opublikować swoją pierwszą aplikację |[Jak skonfigurować serwer Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-enable.md)<br /><br />[Instrukcje instalacji łącznika serwera Proxy aplikacji w trybie dyskretnym](manage-apps/application-proxy-register-connector-powershell.md)<br /><br />[Instrukcje publikowania aplikacji przy użyciu serwera Proxy aplikacji](manage-apps/application-proxy-publish-azure-portal.md)<br /><br />[Jak korzystać z własnej nazwy domeny](manage-apps/application-proxy-configure-custom-domain.md) |
| Jak włączyć pojedynczego logowania jednokrotnego i warunkowego dostępu dla aplikacji opublikowanych przy użyciu serwera Proxy aplikacji |[Logowanie jednokrotne przy użyciu serwera Proxy aplikacji](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)<br /><br />[Dostęp warunkowy i serwera Proxy aplikacji](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| Wskazówki dotyczące sposobu używania serwera Proxy aplikacji w następujących scenariuszach |[Jak obsługiwać natywne aplikacje klienckie](manage-apps/application-proxy-configure-native-client-application.md)<br /><br />[Sposób obsługi aplikacji obsługujących oświadczenia](manage-apps/application-proxy-configure-for-claims-aware-applications.md)<br /><br />[Sposób obsługi aplikacji publikowanych w oddzielnych sieciach i lokalizacje](manage-apps/application-proxy-connector-groups.md) |
| Przewodnik rozwiązywania problemów dla serwera Proxy aplikacji |[Przewodnik rozwiązywania problemów dla serwera Proxy aplikacji](manage-apps/application-proxy-troubleshoot.md) |

Serwer Proxy aplikacji jest dostępna dla wszystkich wersji programu Azure AD dla maksymalnie 10 aplikacji na użytkownika. [Usługa Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) obsługuje aplikacje bez ograniczeń. Jeśli Twoja organizacja ma [usługi Azure AD podstawowa](https://azure.microsoft.com/pricing/details/active-directory/) lub [usługi Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), a następnie możesz [używanie grup do udzielania dostępu do aplikacji](#managing-access-to-applications).

Użytkownik może również zainteresować się [usług domenowych Azure AD](../active-directory-domain-services/active-directory-ds-overview.md), co pozwala na migrowanie aplikacji lokalnych do platformy Azure nadal spełniając potrzeby tożsamości poszczególnych aplikacji.

### <a name="enabling-single-sign-on-between-azure-ad-and-on-premises-ad"></a>Włączanie logowania jednokrotnego między usługą Azure AD i lokalnej usługi AD
Jeśli organizacja ma Windows Server Active Directory w środowisku lokalnym, wraz z usługi Azure Active Directory w chmurze, a następnie będzie prawdopodobnie chcesz włączyć logowanie jednokrotne między tymi dwoma systemami. Azure AD Connect (narzędzie integruje się ze sobą te dwa systemy) oferuje wiele opcji do konfigurowania logowania jednokrotnego: ustanowić Federację z usługą AD FS lub innego dostawcy federacyjnego lub włączanie synchronizacji haseł.

| Artykuł przewodnik |  |
|:---:| --- |
| Omówienie opcje logowania jednokrotnego jest oferowana w Azure AD Connect, a także informacje na temat zarządzania środowisk hybrydowych |[Logowanie użytkownika na temat opcji w usłudze Azure AD Connect](active-directory-aadconnect-user-signin.md) |
| Ogólne wskazówki dotyczące zarządzania środowiskami zarówno lokalnej usługi Active Directory i Azure Active Directory |[Zagadnienia dotyczące projektowania tożsamości hybrydowej platformy Azure AD](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md) |
| Wskazówki na temat korzystania z synchronizacji haseł w celu włączenia funkcji logowania jednokrotnego |[Implementowanie synchronizacji haseł z usługą Azure AD Connect](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)<br /><br />[Rozwiązywanie problemów z synchronizacją haseł](https://support.microsoft.com/en-us/kb/2855271) |
| Wskazówki na temat korzystania z funkcji zapisywania zwrotnego haseł w celu włączenia funkcji logowania jednokrotnego |[Wprowadzenie do zarządzania hasłami w usłudze Azure AD](authentication/quickstart-sspr.md)<br /><br />[Rozwiązywanie problemów z zapisywaniem zwrotnym haseł](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Wskazówki dotyczące przy użyciu dostawców tożsamości innych firm, aby włączyć logowanie Jednokrotne |[Lista dostawców zgodne tożsamości innych firm, których można użyć, aby włączyć logowanie jednokrotne](https://aka.ms/ssoproviders) |
| Jak użytkownicy systemu Windows 10 można wykorzystać zalety logowania jednokrotnego za pośrednictwem usługi Azure AD Join |[Rozszerzanie możliwości chmury do systemu Windows 10 urządzeń za pomocą usługi Azure Active Directory Join](active-directory-azureadjoin-overview.md) |

Program Azure AD Connect jest dostępna dla [wszystkie wersje usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Usługa Azure AD samoobsługowego resetowania hasła jest dostępna dla [usługi Azure AD podstawowa](https://azure.microsoft.com/pricing/details/active-directory/) i [usługi Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Zapisywanie zwrotne haseł do lokalnej usługi AD jest [usługi Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkcji.

### <a name="conditional-access-enforce-additional-security-requirements-for-high-risk-apps"></a>Dostęp warunkowy: Wymuszanie dodatkowe wymagania dotyczące zabezpieczeń dla aplikacji o wysokim ryzyku
Po skonfigurowaniu logowania jednokrotnego do aplikacji i zasobów, możesz dodatkowo zabezpieczyć aplikacji zawierających poufne dane przez wymuszenie stałego specyficzne wymagania zabezpieczeń w każdym logowaniu się do tej aplikacji. Na przykład można użyć usługi Azure AD do zapotrzebowania, że wszelki dostęp do konkretnej aplikacji zawsze należy wymagać uwierzytelniania wieloskładnikowego, niezależnie od tego, czy ta aplikacja innately obsługuje tę funkcję. Innym typowym przykładem dostępu warunkowego jest wymagane, użytkownicy były połączone zaufanych sieci organizacji, aby uzyskać dostęp do aplikacji szczególnie wrażliwe.

| Artykuł przewodnik |  |
|:---:| --- |
| Wprowadzenie do funkcji dostępu warunkowego oferowanych w usłudze Azure AD, Office 365 i Intune |[Zarządzanie ryzykiem przy użyciu dostępu warunkowego](active-directory-conditional-access-azure-portal.md) |
| Jak włączyć dostęp warunkowy dla następujących typów zasobów |[Dostęp warunkowy dla aplikacji SaaS](active-directory-conditional-access-azure-portal-get-started.md)<br /><br />[Dostęp warunkowy dla usługi Office 365](active-directory-conditional-access-device-policies.md)<br /><br />[Dostęp warunkowy dla aplikacji lokalnych](active-directory-conditional-access-azure-portal.md)<br /><br />[Dostęp warunkowy dla aplikacji lokalnych opublikowanych za pośrednictwem serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| Jak rejestrować urządzenia z usługą Azure Active Directory, aby włączyć zasady dostępu warunkowego opartego na urządzeniach |[Omówienie usługi Azure Active Directory rejestracji urządzenia](active-directory-conditional-access-device-registration-overview.md)<br /><br />[Urządzenia Windows przyłączone do włączania automatycznej rejestracji urządzeń dla domeny](active-directory-conditional-access-automatic-device-registration.md)<br />— [Urządzeń kroki dla Windows 8.1](active-directory-conditional-access-automatic-device-registration-setup.md)<br />— [Urządzeń kroki dla Windows 7](active-directory-conditional-access-automatic-device-registration-setup.md) |

| Jak używać aplikacji Microsoft Authenticator na potrzeby weryfikacji dwuetapowej | [Aplikację Microsoft Authenticator](authentication/end-user/current/microsoft-authenticator-app-how-to.md) |

Dostęp warunkowy jest [usługi Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkcji.

## <a name="apps--azure-ad"></a>Aplikacje i usługi Azure AD
### <a name="cloud-discovery-find-which-saas-apps-are-being-used-in-your-organization"></a>Rozwiązanie cloud Discovery: Znajdowanie, aplikacje SaaS, które są używane w Twojej organizacji
Rozwiązanie cloud Discovery analizuje dzienniki ruchu względem wykazu aplikacji w chmurze Microsoft Cloud App Security ponad 16 000 chmury, aplikacje, które są pozycjonowane i oceniane na podstawie których ponad 70 czynników ryzyka, aby zapewnić ciągły wgląd w chmurze używasz, niezatwierdzonych przez dział IT oraz ryzykiem W tle stwarza IT w Twojej organizacji.

| Artykuł przewodnik |  |
|:---:| --- |
| Ogólne omówienie sposobu działania |[Konfigurowanie rozwiązania Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) |


### <a name="automatically-provision-and-deprovision-user-accounts-in-saas-apps"></a>Automatyczne aprowizowanie i anulować aprowizację kont użytkowników w aplikacjach SaaS
Automatyzacja tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach SaaS, takich jak Dropbox, Salesforce, ServiceNow i nie tylko. Zgodne i zsynchronizuj istniejące tożsamości między usługami Azure AD oraz aplikacje SaaS i kontroli dostępu, automatycznie, gdy użytkownicy opuszczają organizację, wyłączając kont.

| Artykuł przewodnik |  |
|:---:| --- |
| Dowiedz się więcej o tym, jak działa i odpowiedzi na często zadawane pytania |[Automatyzowanie użytkownik aprowizacji i anulowania obsługi do aplikacji SaaS](active-directory-saas-app-provisioning.md) |
| Konfigurowanie odwzorowania informacji między usługą Azure AD i aplikacji SaaS |[Dostosowywanie mapowań atrybutów](active-directory-saas-customizing-attribute-mappings.md)<br><br>[Pisanie wyrażeń do mapowania atrybutów](active-directory-saas-writing-expressions-for-attribute-mappings.md) |
| Jak włączyć automatyczne Inicjowanie obsługi do wszystkich aplikacji, która obsługuje protokół Standard SCIM |[Skonfigurować dowolną aplikację SCIM-Enabled automatyczna Aprowizacja użytkowników](manage-apps/use-scim-to-provision-users-and-groups.md) |
| Jak raportować i rozwiązywanie problemów z aprowizowaniem użytkowników |[Raporty dotyczące automatycznej aprowizacji użytkowników](active-directory-saas-provisioning-reporting.md)<br><br>[Rozwiązywanie problemów z aprowizowaniem użytkowników](active-directory-application-provisioning-content-map.md) |
| Limit, który pobiera przygotowany do aplikacji na podstawie ich wartości atrybutu |[Filtrami zakresu](active-directory-saas-scoping-filters.md) |

Inicjowanie obsługi użytkowników automatyczne jest dostępna dla wszystkich wersji programu Azure AD dla maksymalnie 10 aplikacji na użytkownika. [Usługa Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) obsługuje aplikacje bez ograniczeń. Jeśli Twoja organizacja ma [usługi Azure AD podstawowa](https://azure.microsoft.com/pricing/details/active-directory/) lub [usługi Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), a następnie możesz [używanie grup do zarządzania, użytkowników, którzy aprowizowany](#managing-access-to-applications).

### <a name="building-applications-that-integrate-with-azure-ad"></a>Tworzenie aplikacji, które integrują się z usługą Azure AD
Jeśli Twoja organizacja jest tworzenie konserwacja line-of-business (LoB) aplikacji lub jeśli jesteś deweloperem aplikacji ze swoimi klientami, którzy korzystają z usługi Azure Active Directory, może pomóc w ramach następujących samouczków możesz zintegrować swoje aplikacje z usługą Azure AD.

| Artykuł przewodnik |  |
|:---:| --- |
| Wskazówki dla informatyków i deweloperów aplikacji dotyczących integrowania aplikacji z usługą Azure AD |[IT przewodnik dla informatyków do tworzenia aplikacji dla usługi Azure AD](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[Przewodnik dewelopera usługi Azure Active Directory](active-directory-developers-guide.md) |
| Jak aplikacji producenci mogą dodawać swoje aplikacje do galerii aplikacji usługi Azure AD |[Wyświetlanie listy aplikacji w galerii aplikacji usługi Azure Active Directory](active-directory-app-gallery-listing.md) |
| Jak zarządzać dostępem do aplikacji opracowanych przy użyciu usługi Azure Active Directory |[Jak włączyć przypisanie użytkownika dla aplikacji opracowanych](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[Przypisywanie użytkowników do aplikacji](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[Przypisywanie grup do aplikacji](active-directory-applications-guiding-developers-assigning-groups.md) |

Jeśli tworzysz aplikacje dla użytkowników, może Cię zainteresować przy użyciu [usługi Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) tak, aby nie trzeba tworzyć własne systemu tożsamości, aby zarządzać użytkownikami. [Dowiedz się więcej](../active-directory-b2c/active-directory-b2c-overview.md).

## <a name="managing-access-to-applications"></a>Zarządzanie dostępem do aplikacji
### <a name="using-groups-and-self-service-to-manage-who-has-access-to-which-apps"></a>Korzystanie z grup i Samoobsługowe zarządzanie, kto ma dostęp do aplikacji, które
Aby ułatwić zarządzanie, którzy powinni mieć dostęp do określonych zasobów, usługi Azure Active Directory umożliwia ustawianie przydziałów i uprawnienia w dużej skali przy użyciu grup. IT mają możliwość włączenia funkcji samoobsługi, dzięki czemu użytkownicy mogą po prostu zażądać uprawnień, kiedy ich potrzebują.

| Artykuł przewodnik |  |
|:---:| --- |
| Omówienie funkcji usługi Azure AD access management |[Wprowadzenie do zarządzania dostępem do aplikacji](manage-apps/what-is-access-management.md)<br /><br />[Jak działa zarządzanie dostępem w usłudze Azure AD](fundamentals/active-directory-manage-groups.md)<br /><br />[Jak używać grup do zarządzania dostępem do aplikacji SaaS](users-groups-roles/groups-saasapps.md) |
| Włącz samoobsługowe zarządzanie aplikacji i grup |[Zarządzanie aplikacjami samoobsługi](active-directory-self-service-application-access.md)<br /><br />[Samoobsługowe zarządzanie grupami](users-groups-roles/groups-self-service-management.md) |
| Instrukcje dotyczące konfigurowania grup w usłudze Azure AD |[Jak tworzyć grupy zabezpieczeń](fundamentals/active-directory-groups-create-azure-portal.md)<br /><br />[Jak wyznaczyć właścicielami grupy](fundamentals/active-directory-accessmanagement-managing-group-owners.md)<br /><br />[Jak używać grup "Wszyscy użytkownicy"](active-directory-accessmanagement-dedicated-groups.md) |
| Użyj grup dynamicznych, aby automatycznie wypełnić członkostwa w grupie przy użyciu reguł członkostwa na podstawie atrybutu |[Członkostwa grupy dynamicznej: Zaawansowanych reguł](active-directory-groups-dynamic-membership-azure-portal.md)<br /><br />[Rozwiązywanie problemów z członkostwa w grupach dynamicznych](users-groups-roles/groups-troubleshooting.md) |

Zarządzanie dostępem do aplikacji oparte na grupach jest dostępna dla [usługi Azure AD podstawowa](https://azure.microsoft.com/pricing/details/active-directory/) i [usługi Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Samoobsługowe zarządzanie grupami, zarządzanie aplikacjami samoobsługi i grupy dynamiczne są [usługi Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkcji.

### <a name="b2b-collaboration-enable-partner-access-to-applications"></a>Współpraca B2B: Włączanie dostępu partnerów do aplikacji
Jeśli firmie nawiązała współpracę z innymi firmami, jest prawdopodobne, że trzeba zarządzać dostępu partnerów do aplikacji firmowych. Active Directory B2B współpracy w usłudze Azure umożliwia łatwe i bezpieczne udostępnianie aplikacji z partnerami.

| Artykuł przewodnik |  |
|:---:| --- |
| Omówienie różnych usługi Azure AD funkcji, że można pomocy, takich jak zarządzanie użytkowników zewnętrznych w ramach której partnerzy, klienci itd. |[Porównanie możliwości zarządzania tożsamości zewnętrznych w usłudze Azure AD](active-directory-b2b-compare-external-identities.md) |
| Wprowadzenie do współpracy B2B i jak rozpocząć pracę |[Proste, bezpieczne i chmurze partnerów integracji z usługą Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Współpraca B2B w usłudze Azure Active Directory](active-directory-b2b-collaboration-overview.md) |
| Bardziej zgłębić temat współpracy B2B usługi Azure AD i sposobie jej używania |[Współpraca B2B: Jak to działa](active-directory-b2b-how-it-works.md)<br /><br />[Bieżące ograniczenia współpracy B2B usługi Azure AD](active-directory-b2b-current-limitations.md)<br /><br />[Szczegółowy przewodnik dotyczący użycia współpracy B2B usługi Azure AD](active-directory-b2b-detailed-walkthrough.md) |
| Odwołanie do artykułów zawierających szczegółowe informacje techniczne na temat działania współpracy B2B usługi Azure AD |[Format pliku CSV do dodawania użytkowników z firm partnerskich](active-directory-b2b-references-csv-file-format.md)<br /><br />[Atrybuty użytkownika dotyczy funkcji współpracy B2B usługi Azure AD](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[Format tokenu użytkownika dla użytkowników z firm partnerskich](active-directory-b2b-references-external-user-token-format.md) |

Współpraca B2B jest obecnie dostępna dla [wszystkie wersje usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="access-panel-a-portal-for-accessing-apps-and-self-service-features"></a>Panel dostępu: Portal służący do uzyskiwania dostępu do aplikacji i funkcji samoobsługi
Panel dostępu usługi Azure AD jest, gdzie użytkownicy końcowi mogą uruchamianie ich aplikacji i dostęp do funkcji samoobsługi, umożliwiające zarządzanie swoich aplikacji i członkostw w grupie. Oprócz panelu dostępu do innych opcji do uzyskiwania dostępu do aplikacji z włączoną obsługą logowania jednokrotnego znajdują się na poniższej liście.

| Artykuł przewodnik |  |
|:---:| --- |
| Porównanie różnych opcji dostępnymi na potrzeby wdrażania aplikacji pojedynczego logowania jednokrotnego dla użytkowników |[Wdrażanie usługi Azure AD zintegrowanych aplikacji do użytkowników](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users) |
| Omówienie panelu dostępu i jego przenośnych MyApps równoważne |[Wprowadzenie do panelu dostępu i MyApps](active-directory-saas-access-panel-introduction.md)<br />— [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />— [Dla systemu android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Jak uzyskać dostęp do aplikacji usługi Azure AD w witrynie sieci Web usługi Office 365 |[Za pomocą uruchamianie aplikacji usługi Office 365](https://support.office.com/en-us/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Jak uzyskać dostęp do aplikacji usługi Azure AD z aplikacjami mobilnymi usługi Intune Managed Browser |[Intune Managed Browser](https://technet.microsoft.com/library/dn878029.aspx)<br />— [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />— [Dla systemu android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| Jak uzyskać dostęp do aplikacji usługi Azure AD za pomocą linków bezpośrednich do zainicjowania logowania jednokrotnego |[Wprowadzenie łącza bezpośrednie logowanie jednokrotne do aplikacji](manage-apps/what-is-single-sign-on.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

Panel dostępu jest dostępna dla [wszystkie wersje usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="reports-easily-audit-app-access-changes-and-monitor-sign-ins-to-apps"></a>Raporty: Łatwo audytować zmiany dostępu do aplikacji i monitorowanie logowania do aplikacji
Usługa Azure Active Directory oferuje kilka raportów i alertów ułatwiających monitorowanie organizacji dostęp do aplikacji. Możesz otrzymywać alerty dla nietypowe logowania do aplikacji i możesz śledzić, kiedy i dlaczego zmieniono dostęp użytkowników do aplikacji.

| Artykuł przewodnik |  |
|:---:| --- |
| Omówienie funkcji raportowania w usłudze Azure Active Directory |[Wprowadzenie do raportów usługi Azure AD](active-directory-reporting-getting-started.md) |
| Jak monitorować sesje logowania i użycia aplikacji użytkowników |[Wyświetlanie raportów użycia i dostęp](active-directory-view-access-usage-reports.md) |
| Śledzenie zmian, kto ma dostęp do konkretnej aplikacji |[Zdarzenia raportu inspekcji usługi Azure Active Directory](active-directory-reporting-audit-events.md) |
| Eksportowanie danych z tych raportów do preferowanych narzędzi przy użyciu interfejsu API raportowania |[Wprowadzenie do interfejsu API raportowania usługi Azure AD](active-directory-reporting-api-getting-started.md) |

Aby zobaczyć, które raporty są uwzględniane przy użyciu różnych wersji programu Azure Active Directory, [tutaj](active-directory-view-access-usage-reports.md).

## <a name="see-also"></a>Zobacz także
[Co to jest usługa Azure Active Directory?](fundamentals/active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/)

[Usługa Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/)
