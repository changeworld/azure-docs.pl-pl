---
title: Artykuł indeksu do zarządzania aplikacjami w usłudze Azure Active Directory | Microsoft Azure
description: Dowiedz się, jak dostosować daty wygaśnięcia certyfikatów Federacji i sposobie odnawiania certyfikatów, które wkrótce wygasną.
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
ms.date: 01/26/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: efedbf89a6d15a13db5bc86fef9f270e0c84e13b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="article-index-for-application-management-in-azure-active-directory"></a>Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory
Ta strona zawiera pełną listę wszystkich dokumentów zapisywane o różnych funkcjach związanych z aplikacji w usłudze Azure Active Directory (Azure AD).

Krótkie wprowadzenie do każdego obszaru najważniejszych funkcji, a także wskazówki dotyczące artykułów, które do odczytu, w zależności od tego, jakie informacje szukasz nie istnieje.

## <a name="overview-articles"></a>Artykuły — omówienie
Poniższe są dobrym punkt wyjścia dla osób chcących po prostu krótki opis funkcji zarządzania aplikacjami w usłudze Azure AD.

| Przewodnik artykułu |  |
|:---:| --- |
| Wprowadzenie do zarządzania aplikacji problemy rozwiązuje usługa Azure AD |[Zarządzanie aplikacjami przy użyciu usługi Azure Active Directory (AD)](manage-apps/what-is-application-management.md) |
| Przegląd różnych funkcji w usłudze Azure AD powiązany z włączaniem rejestracji jednokrotnej, określające, kto ma dostęp do aplikacji i jak użytkownicy uruchomią aplikacji |[Dostęp do aplikacji i logowanie jednokrotne w usłudze Azure Active Directory](active-directory-appssoaccess-whatis.md) |
| Przeglądać inne czynności związane z integracji aplikacji usługi Azure AD |[Współdziałanie z aplikacjami usługi Azure Active Directory](manage-apps/plan-an-application-integration.md)<br /><br />[Włączanie rejestracji jednokrotnej do aplikacji SaaS](active-directory-enterprise-apps-manage-sso.md)<br /><br />[Zarządzanie dostępem do aplikacji](active-directory-managing-access-to-apps.md) |
| Techniczne wyjaśnienie, w jaki aplikacje są reprezentowane w usłudze Azure AD |[Metody i przyczyny dodawania aplikacji do usługi Azure AD](active-directory-how-applications-are-added.md) |

## <a name="troubleshooting-articles"></a>Rozwiązywanie problemów z artykułów
Ta sekcja zawiera szybki dostęp do odpowiednie instrukcje dotyczące rozwiązywania problemów. Więcej informacji na temat każdego obszaru funkcji można znaleźć w pozostałej części tej strony.

| Obszar funkcji |  |
|:---:| --- |
| Federacyjne logowanie jednokrotne |[Rozwiązywanie problemów z systemem SAML logowania jednokrotnego](active-directory-saml-debugging.md) |
| Oparte na hasłach rejestracji jednokrotnej |[Rozwiązywanie problemów z rozszerzeniem Panel dostępu dla programu Internet Explorer](active-directory-saas-ie-troubleshooting.md) |
| Serwer proxy aplikacji |[Przewodnik rozwiązywania problemów serwera Proxy aplikacji](active-directory-application-proxy-troubleshoot.md) |
| Logowanie jednokrotne między lokalnej usługi AD i Azure AD |[Rozwiązywanie problemów z synchronizacji skrótu hasła](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md#troubleshoot-password-hash-synchronization)<br /><br />[Rozwiązywanie problemów z zapisywaniem zwrotnym haseł](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Członkostwa w grupach dynamiczne |[Rozwiązywanie problemów z członkostwa w grupach dynamiczne](active-directory-accessmanagement-troubleshooting.md) |

## <a name="single-sign-on-sso"></a>Logowanie jednokrotne
### <a name="federated-single-sign-on-sign-into-many-apps-using-one-identity"></a>Federacyjne logowanie jednokrotne: Logowania do wielu aplikacji przy użyciu jednej tożsamości
Logowania jednokrotnego pozwala użytkownikom na dostęp do różnych aplikacji i usług przy użyciu tylko jeden zestaw poświadczeń. Federacja znajduje się jedna metoda, za pomocą których można włączyć logowanie jednokrotne. Gdy użytkownicy próbują zalogować się do aplikacji federacyjnych, będzie spowoduje przekierowanie do organizacji oficjalnego strony logowania renderowana przez usługę Azure Active Directory, a następnie przekierowany do aplikacji po pomyślnym uwierzytelnieniu.

| Przewodnik artykułu |  |
|:---:| --- |
| Wprowadzenie do Federacji i inne typy logowania jednokrotnego |[Logowanie jednokrotne z usługą Azure AD](active-directory-appssoaccess-whatis.md) |
| Tysiące aplikacji SaaS, które są wstępnie zintegrowane z usługą Azure AD z uproszczony czynności konfiguracyjnych rejestracji jednokrotnej |[Wprowadzenie do korzystania z galerii aplikacji Azure AD](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[Pełną listę wstępnie zintegrowanych aplikacji, które obsługują federacyjnego](active-directory-saas-tutorial-list.md)<br /><br />[Jak dodać aplikację do galerii aplikacji Azure AD](active-directory-app-gallery-listing.md) |
| Więcej niż 150 samouczków aplikacji o tym, jak skonfigurować logowanie jednokrotne dla aplikacji takich jak [Salesforce](active-directory-saas-salesforce-tutorial.md), [ServiceNow](active-directory-saas-servicenow-tutorial.md), [usługi Google Apps](active-directory-saas-google-apps-tutorial.md), [produktu Workday](active-directory-saas-workday-tutorial.md)i o wiele więcej. |[Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md) |
| Jak ręcznie skonfigurować i dostosować jednej konfiguracji logowania jednokrotnego |[Jak można skonfigurować federacyjne logowanie jednokrotne do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](application-config-sso-how-to-configure-federated-sso-non-gallery.md)<br /><br />[Dostosowywanie oświadczeń wydanych w tokenie SAML dla wstępnie zintegrowanych aplikacji](active-directory-saml-claims-customization.md) |
| Podręcznik rozwiązywania problemów dotyczących aplikacji federacyjnych, które używają protokołu SAML |[Rozwiązywanie problemów z systemem SAML logowania jednokrotnego](active-directory-saml-debugging.md) |
| Jak skonfigurować datę wygaśnięcia certyfikatu aplikacji i jak odnowić certyfikaty użytkownika |[Zarządzanie certyfikatami dla federacyjnego logowania jednokrotnego w usłudze Azure Active Directory](active-directory-sso-certs.md) |

Federacyjne logowanie jednokrotne jest dostępna dla wszystkich wersji programu Azure AD dla aplikacji do dziesięciu dla poszczególnych użytkowników. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) obsługuje aplikacje nieograniczone. Jeśli Twoja organizacja ma [Azure AD podstawowa](https://azure.microsoft.com/pricing/details/active-directory/) lub [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), a następnie możesz [używanie grup do udzielania dostępu do aplikacji federacyjnych](#managing-access-to-applications).

### <a name="password-based-single-sign-on-account-sharing-and-sso-for-non-federated-apps"></a>Opartego na hasłach rejestracji jednokrotnej: udostępnianie kont i logowania jednokrotnego dla aplikacji niefederacyjnych
Aby włączyć logowanie jednokrotne do aplikacji, które nie obsługują federacyjnych, usługi Azure AD oferuje funkcje zarządzania hasło, które można bezpiecznie przechowywać hasła do aplikacji SaaS i automatyczne logowanie użytkowników do tych aplikacji. Możesz łatwo rozpowszechnić poświadczenia dla nowo utworzonych kont i udostępniać konta team wiele osób. Użytkownicy nie jest konieczna znać kontom one otrzymuje dostęp do poświadczeń.

| Przewodnik artykułu |  |
|:---:| --- |
| Wprowadzenie do jak opartego na hasłach działa usługa rejestracji Jednokrotnej i krótkie omówienie techniczne |[Oparte na hasłach logowanie jednokrotne z usługą Azure AD](active-directory-appssoaccess-whatis.md#password-based-single-sign-on) |
| Podsumowanie scenariuszy związanych z konta do udostępniania i jak te problemy można rozwiązać przez usługę Azure AD |[Udostępnianie kont usługi Azure AD](active-directory-sharing-accounts.md) |
| Automatyczna zmiana hasła dla niektórych aplikacji w regularnych odstępach czasu |[Automatyczne Przerzucanie hasła (wersja zapoznawcza)](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Wdrażanie i rozwiązywanie problemów z przewodników dla używanej wersji programu Internet Explorer rozszerzenie zarządzania hasła usługi Azure AD |[Wdrażanie rozszerzenia Panel dostępu dla programu Internet Explorer przy użyciu zasad grupy](active-directory-saas-ie-group-policy.md)<br /><br />[Rozwiązywanie problemów z rozszerzeniem Panel dostępu dla programu Internet Explorer](active-directory-saas-ie-troubleshooting.md) |

Oparte na hasłach rejestracji jednokrotnej jest dostępna dla wszystkich wersji programu Azure AD dla aplikacji do dziesięciu dla poszczególnych użytkowników. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) obsługuje aplikacje nieograniczone. Jeśli Twoja organizacja ma [Azure AD podstawowa](https://azure.microsoft.com/pricing/details/active-directory/) lub [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), a następnie możesz [używanie grup do udzielania dostępu do aplikacji](#managing-access-to-applications). To hasło automatycznego przerzucania [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkcji.

### <a name="app-proxy-single-sign-on-and-remote-access-to-on-premises-applications"></a>Serwer Proxy aplikacji: Pojedynczego logowania jednokrotnego i dostęp zdalny do aplikacji lokalnych
Jeśli masz aplikacje w sieci prywatnej, które muszą być dostępne dla użytkowników i urządzeń spoza sieci, można użyć serwera Proxy aplikacji usługi Azure AD, aby włączyć bezpieczny, zdalny dostęp do tych aplikacji.

| Przewodnik artykułu |  |
|:---:| --- |
| Omówienie serwera Proxy aplikacji usługi Azure AD oraz sposób jej działania |[Bezpieczny dostęp zdalny do aplikacji lokalnych](manage-apps/application-proxy.md) |
| Samouczki na sposób konfigurowania serwera Proxy aplikacji i jak opublikować pierwszej aplikacji |[Jak skonfigurować serwera Proxy aplikacji usługi Azure AD](active-directory-application-proxy-enable.md)<br /><br />[Jak zainstalować łącznik serwera Proxy aplikacji w trybie dyskretnym](active-directory-application-proxy-silent-installation.md)<br /><br />[Sposób publikowania aplikacji przy użyciu serwera Proxy aplikacji](application-proxy-publish-azure-portal.md)<br /><br />[Jak korzystać z własnej nazwy domeny](active-directory-application-proxy-custom-domains.md) |
| Jak włączyć jednego logowania i warunkowego dostępu dla aplikacji opublikowane przy użyciu serwera Proxy aplikacji |[Single-sign-on z serwerem Proxy aplikacji](active-directory-application-proxy-sso-using-kcd.md)<br /><br />[Dostęp warunkowy i serwera Proxy aplikacji](application-proxy-enable-remote-access-sharepoint.md) |
| Wskazówki dotyczące sposobu używania serwera Proxy aplikacji w następujących scenariuszach |[Jak obsługiwać aplikacje Native Client](active-directory-application-proxy-native-client.md)<br /><br />[Jak obsługiwać aplikacje obsługujące oświadczenia](active-directory-application-proxy-claims-aware-apps.md)<br /><br />[Jak obsługiwać aplikacji publikowanych w odrębnych sieci i lokalizacje](active-directory-application-proxy-connectors-azure-portal.md) |
| Podręcznik rozwiązywania problemów dotyczących serwera Proxy aplikacji |[Przewodnik rozwiązywania problemów serwera Proxy aplikacji](active-directory-application-proxy-troubleshoot.md) |

Serwer Proxy aplikacji jest dostępna dla wszystkich wersji programu Azure AD dla aplikacji do dziesięciu dla poszczególnych użytkowników. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) obsługuje aplikacje nieograniczone. Jeśli Twoja organizacja ma [Azure AD podstawowa](https://azure.microsoft.com/pricing/details/active-directory/) lub [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), a następnie możesz [używanie grup do udzielania dostępu do aplikacji](#managing-access-to-applications).

Można go również zainteresowana [usług domenowych Azure AD](../active-directory-domain-services/active-directory-ds-overview.md), co pozwala na migracji aplikacji lokalnej na platformie Azure nadal spełniając wymagania tożsamości tych aplikacji.

### <a name="enabling-single-sign-on-between-azure-ad-and-on-premises-ad"></a>Włączanie rejestracji jednokrotnej między usługą Azure AD i lokalnej usłudze AD
Jeśli organizacja ma Windows Server Active Directory lokalnie wraz z usługi Azure Active Directory w chmurze, a następnie będzie prawdopodobnie chcesz włączyć logowanie jednokrotne między tymi dwoma systemami. Azure AD Connect (narzędzie integruje się ze sobą te dwa systemy) udostępnia wiele opcji konfigurowania rejestracji jednokrotnej: ustanowienia federacyjnej usług AD FS lub innego dostawcy federacyjnego lub włączanie synchronizacji haseł.

| Przewodnik artykułu |  |
|:---:| --- |
| Omówienie jednej opcji logowania jednokrotnego oferowany Azure AD Connect, a także informacje na temat zarządzania środowisk hybrydowych |[Logowanie użytkownika na temat opcji w usłudze Azure AD Connect](active-directory-aadconnect-user-signin.md) |
| Ogólne wskazówki dotyczące zarządzania środowisk zarówno lokalnej usługi Active Directory i Azure Active Directory |[Zagadnienia dotyczące projektowania tożsamości hybrydowej usługi Azure AD](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md) |
| Wskazówki dotyczące korzystania z synchronizacji haseł w celu włączenia funkcji logowania jednokrotnego |[Implementowanie synchronizacji haseł w usłudze Azure AD Connect](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)<br /><br />[Rozwiązywanie problemów z synchronizacją haseł](https://support.microsoft.com/en-us/kb/2855271) |
| Wskazówki dotyczące używania funkcji zapisywania zwrotnego haseł do włączenia funkcji logowania jednokrotnego |[Wprowadzenie do zarządzania hasłami w usłudze Azure AD](authentication/quickstart-sspr.md)<br /><br />[Rozwiązywanie problemów z zapisywaniem zwrotnym haseł](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Wskazówki dotyczące włączenia funkcji logowania jednokrotnego przy użyciu dostawcy tożsamości innych firm |[Lista dostawców zgodne tożsamości innych firm, które mogą służyć do Włącz rejestrację jednokrotną](https://aka.ms/ssoproviders) |
| Jak użytkownicy systemu Windows 10 można korzystać z zalet logowania jednokrotnego za pośrednictwem usługi Azure AD Join |[Rozszerzanie możliwości chmury do systemu Windows 10 urządzeń za pomocą usługi Azure Active Directory Join](active-directory-azureadjoin-overview.md) |

Azure AD Connect jest dostępna dla [wszystkie wersje usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Azure AD samoobsługowego resetowania hasła jest dostępna dla [Azure AD podstawowa](https://azure.microsoft.com/pricing/details/active-directory/) i [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Zapisywanie zwrotne haseł do lokalnej usługi AD jest [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkcji.

### <a name="conditional-access-enforce-additional-security-requirements-for-high-risk-apps"></a>Dostępu warunkowego: Dodatkowe wymagania dotyczące zabezpieczeń dla aplikacji o wysokim ryzyku wymuszenia
Po skonfigurowaniu rejestracji jednokrotnej do aplikacji i zasobów, można następnie dodatkowo zabezpieczyć aplikacji zawierających poufne dane przez wymuszenie specyficzne wymagania zabezpieczeń na każdym zalogować się do tej aplikacji. Na przykład można użyć usługi Azure AD na żądanie dostępu do danej aplikacji będzie zawsze wymagają uwierzytelniania wieloskładnikowego, niezależnie od tego, czy danej aplikacji innately obsługuje te funkcje. Inny typowy przykład dostępu warunkowego jest wymagają, aby użytkownicy można połączony zaufanej sieci organizacji, aby uzyskać dostęp do aplikacji szczególnie cenne.

| Przewodnik artykułu |  |
|:---:| --- |
| Wprowadzenie do funkcji dostępu warunkowego oferowane przez usługi Azure AD, usługi Office 365 i Intune |[Zarządzanie ryzykiem przy użyciu dostępu warunkowego](active-directory-conditional-access-azure-portal.md) |
| Sposób włączania dostępu warunkowego dla następujących typów zasobów |[Dostęp warunkowy dla aplikacji SaaS](active-directory-conditional-access-azure-portal-get-started.md)<br /><br />[Dostęp warunkowy dla usługi Office 365](active-directory-conditional-access-device-policies.md)<br /><br />[Dostęp warunkowy do aplikacji lokalnych](active-directory-conditional-access-azure-portal.md)<br /><br />[Dostęp warunkowy do aplikacji lokalnych opublikowane za pośrednictwem serwera Proxy aplikacji usługi Azure AD](application-proxy-enable-remote-access-sharepoint.md) |
| Jak zarejestrować urządzenia z usługą Azure Active Directory, aby włączyć zasady dostępu warunkowego opartego na urządzeniu |[Omówienie usługi Azure Active Directory rejestracji urządzenia](active-directory-conditional-access-device-registration-overview.md)<br /><br />[Włączanie automatycznej rejestracji urządzeń dla domeny przyłączone do urządzeń z systemem Windows](active-directory-conditional-access-automatic-device-registration.md)<br />— [Urządzeń kroki dla Windows 8.1](active-directory-conditional-access-automatic-device-registration-setup.md)<br />— [Urządzeń kroki dla systemu Windows 7](active-directory-conditional-access-automatic-device-registration-setup.md) |

| Sposób użycia aplikacji Authenticator firmy Microsoft na potrzeby weryfikacji dwuetapowej | [Uwierzytelniania firmy Microsoft](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

Dostęp warunkowy jest [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkcji.

## <a name="apps--azure-ad"></a>Aplikacje i usługi Azure AD
### <a name="cloud-app-discovery-find-which-saas-apps-are-being-used-in-your-organization"></a>Usługa cloud App Discovery: Znajdowanie aplikacji SaaS, które są używane w organizacji
Usługa cloud App Discovery pomaga działów IT informacje aplikacje SaaS, które są używane w całej organizacji. Go Mierz użycie aplikacji i popularność, którego nie można określić aplikacje, które będą korzystać z najbardziej przed sterowane przez dział IT kontrolę i zostanie zintegrowanych z usługą Azure AD.

| Przewodnik artykułu |  |
|:---:| --- |
| Ogólne omówienie jak to działa |[Znajdowanie niezatwierdzona aplikacji w chmurze z usługi Cloud App Discovery](manage-apps/cloud-app-discovery.md) |
| Bardziej zgłębić temat do jej działania, o odpowiedzi na pytania dotyczące ochrony prywatności |[Bezpieczeństwo i zagadnienia dotyczące ochrony prywatności](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) |
| Często zadawane pytania |[Często zadawane pytania dotyczące Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |
| Samouczki dotyczące wdrażania Cloud App Discovery |[Przewodnik dotyczący wdrażania zasad grupy](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)<br /><br />[Przewodnik wdrażania programu System Center](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)<br /><br />[Instalowanie na serwerach Proxy z portami niestandardowe](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| Dziennik zmian dla aktualizacji agenta Cloud App Discovery |[Dziennik zmian](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx) |

Usługa cloud App Discovery jest [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkcji.

### <a name="automatically-provision-and-deprovision-user-accounts-in-saas-apps"></a>Automatycznie udostępnić i anulowanie zastrzeżenia kont użytkowników w aplikacji SaaS
Umożliwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacji SaaS, takich jak Dropbox, Salesforce, ServiceNow i inne. Zgodne i synchronizowanie istniejących tożsamości między usługą Azure AD i SaaS aplikacji i kontroli dostępu przez wyłączenie konta automatycznie, gdy użytkownicy opuszczają organizację.

| Przewodnik artykułu |  |
|:---:| --- |
| Informacje dotyczące sposobu działania i odpowiedzi na często zadawane pytania |[Automatyzowanie użytkownik aprowizacji i anulowania obsługi do aplikacji SaaS](active-directory-saas-app-provisioning.md) |
| Skonfiguruj odwzorowania informacji między usługą Azure AD i aplikacji SaaS |[Dostosowywanie mapowań atrybutów](active-directory-saas-customizing-attribute-mappings.md)<br><br>[Tworzenie wyrażeń na potrzeby mapowań atrybutów](active-directory-saas-writing-expressions-for-attribute-mappings.md) |
| Jak włączyć automatyczne Inicjowanie obsługi do wszystkich aplikacji, który obsługuje protokół SCIM |[Skonfiguruj automatyczne Inicjowanie obsługi użytkowników do wszystkich aplikacji SCIM-Enabled](active-directory-scim-provisioning.md) |
| Raport dotyczący i rozwiązywanie problemów z Inicjowanie obsługi użytkowników |[Raporty dotyczące użytkownika automatycznego inicjowania obsługi administracyjnej.](active-directory-saas-provisioning-reporting.md)<br><br>[Rozwiązywanie problemów z Inicjowanie obsługi użytkowników](active-directory-application-provisioning-content-map.md) |
| Limit, który pobiera przygotowana do aplikacji na podstawie ich wartości atrybutu |[Filtrami zakresów](active-directory-saas-scoping-filters.md) |

Inicjowanie obsługi użytkowników automatycznego jest dostępna dla wszystkich wersji programu Azure AD dla aplikacji do dziesięciu dla poszczególnych użytkowników. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) obsługuje aplikacje nieograniczone. Jeśli Twoja organizacja ma [Azure AD podstawowa](https://azure.microsoft.com/pricing/details/active-directory/) lub [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), a następnie możesz [używanie grup do zarządzania, uzyskiwanie obsługi administracyjnej użytkowników, którzy](#managing-access-to-applications).

### <a name="building-applications-that-integrate-with-azure-ad"></a>Tworzenie aplikacji, które integrują się z usługą Azure AD
Jeśli Twoja organizacja jest tworzenie lub obsługa — biznesowych (LoB) aplikacji lub jeśli Deweloper aplikacji z klientami, którzy korzystają z usługi Azure Active Directory, pomogą następujące samouczki aplikacji można zintegrować z usługą Azure AD.

| Przewodnik artykułu |  |
|:---:| --- |
| Wskazówki dla informatyków i deweloperów aplikacji na integrowanie aplikacji z usługą Azure AD |[IT Pro w przewodniku do opracowywania aplikacji dla usługi Azure AD](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[Przewodnik dewelopera usługi Azure Active Directory](active-directory-developers-guide.md) |
| Jak do aplikacji dostawcy aplikacje można dodawać ich do galerii aplikacji Azure AD |[Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](active-directory-app-gallery-listing.md) |
| Jak zarządzać dostępem do aplikacje przy użyciu usługi Azure Active Directory |[Jak włączyć przypisanie użytkownika do aplikacje](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[Przypisywanie użytkowników do aplikacji](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[Przypisywanie grupy aplikacji](active-directory-applications-guiding-developers-assigning-groups.md) |

W przypadku tworzenia aplikacji dla użytkownika, może Cię zainteresować przy użyciu [usługi Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) tak, aby nie trzeba opracowanie własnego systemu tożsamości do zarządzania przez użytkowników. [Dowiedz się więcej](../active-directory-b2c/active-directory-b2c-overview.md).

## <a name="managing-access-to-applications"></a>Zarządzanie dostępem do aplikacji
### <a name="using-groups-and-self-service-to-manage-who-has-access-to-which-apps"></a>Przy użyciu grup i samoobsługowego zarządzania, kto ma dostęp do aplikacji, które
Aby ułatwić zarządzanie, kto ma dostęp do określonych zasobów, usługi Azure Active Directory umożliwia ustawianie uprawnień i przydziały na dużą skalę przy użyciu grup. IT mogą wybrać włączyć funkcje samoobsługi, dzięki czemu użytkownicy mogą zażądać uprawnień po prostu, gdy są one wymagane.

| Przewodnik artykułu |  |
|:---:| --- |
| Omówienie funkcji zarządzania dostęp do usługi Azure AD |[Wprowadzenie do zarządzania dostępem do aplikacji](active-directory-managing-access-to-apps.md)<br /><br />[Jak działa Zarządzanie dostępu w usłudze Azure AD](active-directory-manage-groups.md)<br /><br />[Jak używać grup do zarządzania dostępem do aplikacji SaaS](active-directory-accessmanagement-group-saasapps.md) |
| Włącz samoobsługowe zarządzanie aplikacji i grup |[Zarządzanie aplikacjami samoobsługi](active-directory-self-service-application-access.md)<br /><br />[Zarządzanie grupami samoobsługi](active-directory-accessmanagement-self-service-group-management.md) |
| Instrukcje dotyczące konfigurowania grup w usłudze Azure AD |[Tworzenie grup zabezpieczeń](active-directory-groups-create-azure-portal.md)<br /><br />[Jak wyznaczyć właścicielami grupy](active-directory-accessmanagement-managing-group-owners.md)<br /><br />[Jak używać grupie "Wszyscy użytkownicy"](active-directory-accessmanagement-dedicated-groups.md) |
| Użyj grup dynamicznych, które automatycznie wypełnić członkostwa grupy przy użyciu reguł członkostwa na podstawie atrybutu |[Członkostwa w grupie dynamiczne: Zaawansowanych reguł](active-directory-groups-dynamic-membership-azure-portal.md)<br /><br />[Rozwiązywanie problemów z członkostwa w grupach dynamiczne](active-directory-accessmanagement-troubleshooting.md) |

Zarządzanie dostępem na podstawie grupy aplikacji jest dostępna dla [Azure AD podstawowa](https://azure.microsoft.com/pricing/details/active-directory/) i [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Samoobsługowe zarządzanie grupami, zarządzanie aplikacjami samoobsługi i grupami dynamicznymi [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkcji.

### <a name="b2b-collaboration-enable-partner-access-to-applications"></a>Współpraca B2B: Włącz partnera dostęp do aplikacji
Jeśli firma współpracuje z innymi firmami, prawdopodobnie konieczne zarządzanie dostępem partnera do aplikacji firmowych. Azure współpracy B2B Active Directory zapewnia prosty i bezpieczny sposób udostępniać aplikacje partnerów.

| Przewodnik artykułu |  |
|:---:| --- |
| Omówienie usługi Azure AD różnych funkcji, czy można uzyskać pomoc, takich jak zarządzanie użytkowników zewnętrznych partnerzy, klienci itd. |[Porównanie funkcji zarządzania w usłudze Azure AD tożsamości zewnętrznych](active-directory-b2b-compare-external-identities.md) |
| Wprowadzenie do współpracy B2B i jak rozpocząć pracę |[Prosty bezpieczny, chmury partnera integracji z usługą Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Współpraca B2B usługi Azure Active Directory](active-directory-b2b-collaboration-overview.md) |
| Bardziej zgłębić temat do współpracy B2B usługi Azure AD i jak z niego korzystać |[Współpraca B2B: Jak to działa](active-directory-b2b-how-it-works.md)<br /><br />[Bieżące ograniczenia współpracy B2B usługi Azure AD](active-directory-b2b-current-limitations.md)<br /><br />[Szczegółowy przewodnik dotyczący użycia współpracy B2B usługi Azure AD](active-directory-b2b-detailed-walkthrough.md) |
| Odwołanie do artykułów z szczegółowe informacje techniczne dotyczące sposobu działania współpracy B2B usługi Azure AD |[Format pliku CSV do dodawania użytkowników z firm partnerskich](active-directory-b2b-references-csv-file-format.md)<br /><br />[Atrybuty użytkowników dotyczy współpracy B2B usługi Azure AD](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[Format tokenu użytkownika dla użytkowników z firm partnerskich](active-directory-b2b-references-external-user-token-format.md) |

Współpraca B2B jest obecnie dostępna [wszystkie wersje usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="access-panel-a-portal-for-accessing-apps-and-self-service-features"></a>Panel dostępu: Portal służący do uzyskiwania dostępu do aplikacji i funkcji samoobsługi
Panel dostępu usługi Azure AD jest, gdzie użytkownicy końcowi mogą uruchom swoje aplikacje i uzyskiwać dostęp do funkcji samoobsługi, umożliwiające zarządzanie aplikacjami i członkostwa w grupach. Oprócz panelu dostępu inne opcje do uzyskiwania dostępu do aplikacji z włączoną obsługą rejestracji Jednokrotnej znajdują się na poniższej liście.

| Przewodnik artykułu |  |
|:---:| --- |
| Porównanie różne opcje dostępne podczas wdrażania aplikacji rejestracji jednokrotnej dla użytkowników |[Wdrażanie usługi Azure AD zintegrowanych aplikacji dla użytkowników](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) |
| Omówienie panelu dostępu i jego przenośnych MyApps równoważne |[Wprowadzenie do panelu dostępu i MyApps](active-directory-saas-access-panel-introduction.md)<br />— [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />— [Systemu android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Jak uzyskać dostęp do aplikacji Azure AD z witryny sieci Web usługi Office 365 |[Za pomocą usługi Office 365 uruchamiania aplikacji](https://support.office.com/en-us/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Jak uzyskać dostęp do aplikacji Azure AD z aplikacjami mobilnymi usługi Intune Managed Browser |[Intune Managed Browser](https://technet.microsoft.com/library/dn878029.aspx)<br />— [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />— [Systemu android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| Jak uzyskać dostęp do aplikacji usługi Azure AD za pomocą linków bezpośrednich do zainicjowania rejestracji jednokrotnej |[Pobieranie linki bezpośrednie logowania jednokrotnego do aplikacji](active-directory-appssoaccess-whatis.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

Panel dostępu jest dostępna dla [wszystkie wersje usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="reports-easily-audit-app-access-changes-and-monitor-sign-ins-to-apps"></a>Raporty: Łatwo przeprowadzać inspekcję zmian dostępu do aplikacji i monitorowanie logowania do aplikacji
Azure Active Directory oferuje kilka raporty i alerty ułatwiają monitorowanie w organizacji dostęp do aplikacji. Użytkownik może otrzymywać alerty dotyczące nietypowe logowania do aplikacji, a można śledzić, kiedy i dlaczego dostęp użytkowników do aplikacji została zmieniona.

| Przewodnik artykułu |  |
|:---:| --- |
| Omówienie funkcji raportowania w usłudze Azure Active Directory |[Wprowadzenie do korzystania z raportów usługi Azure AD](active-directory-reporting-getting-started.md) |
| Jak monitorować logowania i użycia aplikacji użytkowników |[Wyświetlanie dostępu oraz raporty użycia](active-directory-view-access-usage-reports.md) |
| Śledzenie zmian, kto ma dostęp do konkretnej aplikacji |[Zdarzenia raportów inspekcji usługi Azure Active Directory](active-directory-reporting-audit-events.md) |
| Eksportuj dane z tych raportów do narzędziami preferowanych przy użyciu interfejsu API raportowania |[Wprowadzenie do raportowania interfejsu API usługi Azure AD](active-directory-reporting-api-getting-started.md) |

Aby zobaczyć, które raporty są dołączone do różnych wersjach usługi Azure Active Directory, [kliknij tutaj](active-directory-view-access-usage-reports.md).

## <a name="see-also"></a>Zobacz także
[Co to jest usługa Azure Active Directory?](active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/)

[Uwierzytelnianie wieloskładnikowe platformy Azure](https://azure.microsoft.com/services/multi-factor-authentication/)
