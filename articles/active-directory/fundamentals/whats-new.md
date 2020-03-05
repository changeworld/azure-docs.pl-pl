---
title: Co nowego? Informacje o wersji — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Aby dowiedzieć się o nowych za pomocą usługi Azure Active Directory, takie jak najnowszej wersji, znanych problemów i poprawek błędów przestarzałe funkcje i nadchodzących zmian.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cf12c28dc04d0dbb8a680d45c8d8f5f5faa2d82
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274194"
---
# <a name="whats-new-in-azure-active-directory"></a>What's new in Azure Active Directory?

>Otrzymuj powiadomienia o tym, kiedy należy ponownie odwiedzić Tę stronę pod kątem aktualizacji przez skopiowanie i wklejenie tego adresu URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` do ![ikona czytnika kanału informacyjnego RSS](./media/whats-new/feed-icon-16x16.png) Reader.

Usługa Azure AD odbiera ulepszenia w sposób ciągły. Aby zachować aktualność w zakresie najnowszych zmian, ten artykuł zawiera informacje na temat:

- Zainstalowane najnowsze wersje
- Znane problemy
- Poprawki błędów
- Przestarzałe funkcje
- Plany dotyczące zmian

Ta strona jest aktualizowany co miesiąc, więc ponownie regularnie. Jeśli szukasz elementów, które są starsze niż sześć miesięcy, możesz je znaleźć w archiwum, aby poznać nowości [w Azure Active Directory](whats-new-archive.md).

---

## <a name="february-2020"></a>Luty 2020 r.
 
### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Bezpieczna ocena tożsamości — aktualizacje akcji poprawy usługi MFA

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Funkcja  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Aby odzwierciedlić potrzebę firm, aby zapewnić najwyższy poziom bezpieczeństwa podczas stosowania zasad, które współpracują z firmą, firma Microsoft Security Score usuwa trzy akcje ulepszania w centrum uwierzytelniania wieloskładnikowego (MFA) i dodaje dwa.

Następujące akcje poprawy zostaną usunięte:

- Zarejestruj wszystkich użytkowników usługi MFA
- Wymaganie uwierzytelniania wieloskładnikowego dla wszystkich użytkowników
- Wymagaj uwierzytelniania wieloskładnikowego dla ról uprzywilejowanych usługi Azure AD

Zostaną dodane następujące akcje ulepszania:

- Upewnij się, że wszyscy użytkownicy mogą ukończyć uwierzytelnianie wieloskładnikowe w celu zapewnienia bezpiecznego dostępu
- Wymagaj uwierzytelniania wieloskładnikowego dla ról administracyjnych

Te nowe akcje poprawy będą wymagały zarejestrowania użytkowników lub administratorów usługi MFA w katalogu i ustanowienia odpowiedniego zestawu zasad pasujących do potrzeb organizacji. Głównym celem jest zapewnienie elastyczności, dzięki czemu wszyscy użytkownicy i Administratorzy mogą uwierzytelniać się przy użyciu wielu czynników lub z zapytaniem do weryfikacji tożsamości opartej na ryzyku. Może to mieć formę ustawień domyślnych zabezpieczeń, które umożliwiają firmie Microsoft decydowanie o tym, kiedy należy zadawać użytkownikom uwierzytelnianie MFA, lub mając wiele zasad, które stosują decyzje o zakresie. W ramach tych aktualizacji akcji poprawy zasady ochrony linii bazowej nie będą już uwzględniane w obliczeniach oceniania. [Przeczytaj więcej na temat tego, co jest dostępne w usłudze Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).
 
---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services wybór jednostki SKU

**Typ:** Nowa funkcja  
**Kategoria usługi:** Azure AD Domain Services  
**Możliwość produktu:** Azure AD Domain Services
 
Otrzymaliśmy Opinie, które Azure AD Domain Services klienci chcą zwiększyć elastyczność w wyborze poziomów wydajności dla ich wystąpień. Od 1 lutego 2020 przejdziemy na model dynamiczny (gdzie usługa Azure AD określa wydajność i warstwę cenową na podstawie liczby obiektów) do modelu samodzielnego wyboru. Teraz klienci mogą wybrać warstwę wydajności, która odpowiada ich środowisku. Ta zmiana pozwala również na włączenie nowych scenariuszy, takich jak lasy zasobów, oraz funkcji premium, takich jak codzienne kopie zapasowe. Liczba obiektów jest teraz nieograniczona dla wszystkich jednostek SKU, ale będziemy nadal oferować sugestie dotyczące liczby obiektów dla każdej warstwy.

**Nie jest wymagana żadna bezpośrednia akcja klienta.** W przypadku istniejących klientów warstwa dynamiczna używana w dniu 1 lutego 2020 określa nową warstwę domyślną. W wyniku tej zmiany nie ma wpływu na cenę ani wpływ na wydajność. W przyszłości klienci korzystający z platformy Azure AD DS będą musieli oszacować wymagania dotyczące wydajności, ponieważ zmienią się ich rozmiary katalogów i charakterystyki obciążeń. Przełączenie między warstwami usług będzie nadal trwać bez przestojów i nie będzie już automatycznie przenosić klientów do nowych warstw na podstawie wzrostu ich katalogu. Ponadto nie będzie można zwiększyć cen, a nowe ceny będą wyrównane z naszym bieżącym modelem rozliczeń. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure AD DS SKU](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) i [stronę cennika Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).


---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — luty 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W lutym 2020 dodaliśmy te 31 nowych aplikacji z obsługą Federacji do galerii aplikacji: 

[IamIP platformę patentową](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [chmurę](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial)usług, [ns1 Logowanie jednokrotne dla platformy Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Usługa zabezpieczeń poczty E-mail Barracuda](https://ess.barracudanetworks.com/sso/azure), [raportowanie ABA](https://myaba.co.uk/client-access/signin/auth/msad), [w przypadku kryzysu — portal online](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [BIC projektowanie chmur](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial) [, program](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial) [pszczelarstwa usługi Azure AD Data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial) [oceny promów](https://www.kornferry.com/solutions/kf-digital/kf-assess) [, Verkada,](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial) [Splashtop, Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial) [, EAB](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial) [](https://admin.thulium.com/login/instance) [](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial) [ Wybór dla zespołów](https://links.officeatwork.com/templatechooser-download-teams), [pszczół](https://www.beesy.me/index.php/site/login), [system pomocy technicznej Health](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [mural](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink dla nauczycieli i szkół](https://www.thinglink.com/), [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), WeDo [,](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial) [InvitePeople](https://invitepeople.com/login), [ponowne drukowanie, artykuł Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nowe łączniki aprowizacji w galerii aplikacji usługi Azure AD — luty 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud według Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzacja aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Obsługa kluczy zabezpieczeń usługi Azure AD dla FIDO2 w środowiskach hybrydowych

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Ogłaszamy publiczną wersję zapoznawczą obsługi kluczy zabezpieczeń usługi Azure AD dla FIDO2 w środowiskach hybrydowych. Użytkownicy mogą teraz używać kluczy zabezpieczeń FIDO2 do logowania się do swoich urządzeń z systemem Windows 10 hybrydowej usługi Azure AD i zapewnienia bezproblemowego logowania do zasobów lokalnych i w chmurze. Obsługa środowisk hybrydowych była najczęściej żądaną funkcją od naszych klientów, od momentu pierwszego uruchomienia publicznej wersji zapoznawczej obsługi FIDO2 na urządzeniach dołączonych do usługi Azure AD. Uwierzytelnianie bez hasła przy użyciu zaawansowanych technologii, takich jak biometry oraz Kryptografia klucza publicznego/prywatnego, zapewnia wygodę i łatwość używania, gdy jest to bezpieczne. Korzystając z tej publicznej wersji zapoznawczej, można teraz używać nowoczesnego uwierzytelniania, takiego jak FIDO2 klucze zabezpieczeń, aby uzyskiwać dostęp do tradycyjnych zasobów Active Directory. Aby uzyskać więcej informacji, przejdź do obszaru [Logowanie jednokrotne do zasobów lokalnych](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises). 

Aby rozpocząć, odwiedź stronę [Włączanie kluczy zabezpieczeń FIDO2 dla Twojej dzierżawy](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) , aby uzyskać instrukcje krok po kroku. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Nowe środowisko moje konto jest teraz ogólnie dostępne 

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Mój profil/konto  
**Możliwość produktu:** Środowiska użytkownika końcowego
 
Moje konto — ten, który zatrzymał się w przypadku wszystkich potrzeb związanych z zarządzaniem kontem użytkownika końcowego, jest teraz ogólnie dostępny. Użytkownicy końcowi mogą uzyskiwać dostęp do tej nowej witryny za pośrednictwem adresu URL lub w nagłówku nowego środowiska moje aplikacje. Dowiedz się więcej na temat wszystkich funkcji samoobsługowych oferowanych przez nowe środowisko w [portalu mojego konta](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Adres URL witryny My Account do aktualizowania na myaccount.microsoft.com

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Mój profil/konto  
**Możliwość produktu:** Środowiska użytkownika końcowego
 
Nowe środowisko użytkownika końcowego moje konto będzie aktualizować swój adres URL do https://myaccount.microsoft.com w następnym miesiącu. Znajdź więcej informacji na temat środowiska oraz wszystkie możliwości samoobsługowego konta, które oferuje użytkownikom końcowym w [pomocy portalu mojego konta](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
## <a name="january-2020"></a>Styczeń 2020 r.
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Nowy portal moje aplikacje jest teraz ogólnie dostępny

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** Środowiska użytkownika końcowego
 
Uaktualnij swoją organizację do nowego portalu Moje aplikacje, który jest teraz ogólnie dostępny! Więcej informacji na temat nowego portalu i kolekcji znajduje się w [sekcji Tworzenie kolekcji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Nazwy obszarów roboczych w usłudze Azure AD zostały zmienione na Kolekcje

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Moje aplikacje   
**Możliwość produktu:** Środowiska użytkownika końcowego
 
Obszary robocze, Administratorzy filtrów mogą konfigurować, aby organizować aplikacje użytkowników, będą teraz określane jako kolekcje. Więcej informacji na temat sposobu ich konfigurowania można znaleźć w [sekcji Tworzenie kolekcji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C Rejestracja i logowanie przy użyciu zasad niestandardowych (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C
 
Dzięki rejestrowaniu i rejestrowaniu numerów telefonów deweloperzy i przedsiębiorstwa mogą umożliwić klientom rejestrowanie się i logowanie przy użyciu hasła jednorazowego wysyłanego do numeru telefonu użytkownika za pośrednictwem wiadomości SMS. Ta funkcja umożliwia także zmianę numeru telefonu przez klienta w przypadku utraty dostępu do telefonu. Korzystając z możliwości zasad niestandardowych, rejestracja i logowanie za pomocą telefonu umożliwia deweloperom i przedsiębiorstwom komunikowanie się z nimi przez dostosowanie strony. Dowiedz się [, jak skonfigurować rejestrowanie i logowanie za pomocą zasad niestandardowych w Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nowe łączniki aprowizacji w galerii aplikacji usługi Azure AD — styczeń 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Rozwiązania Zscaler prywatny dostęp](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzacja aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — styczeń 2020

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy
 
W styczniu 2020 dodaliśmy do galerii aplikacji 33 te nowe aplikacje z obsługą Federacji: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [szybka krawędź w chmurze](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://1030-review-develop-3zknud.netlogistik.com/), [SkyKick Cloud Backup for Office 365](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [,](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial) [LeaveBot](https://leavebot.io/#home), [datacamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial) [, TripActions, SmartWork](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [dotcom](https://www.intumit.com/english/SmartWork.html) [-monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite-EBS, PeopleSoft i JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [hostowana MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [yuhu — Platforma zarządzania właściwościami](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB for Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe SAML2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), Maxient on [Manager Software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [rerecenzja](https://portal.coreview.com/), [squelch Cloud 365 Connector](https://laxmi.squelch.io/login), [PingFlow Authentication](https://app-staging.pingview.io/), [PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai](https://app.sandwai.com/), [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), Akari [ Asystent wirtualny](https://akari.io/akari-virtual-assistant/)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Dwa nowe wykrywania ochrony tożsamości

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia
 
Dodaliśmy dwa nowe typy wykrywania połączonego logowania do ochrony tożsamości: podejrzane reguły manipulowania skrzynką odbiorczą i niemożliwa podróż. Te wykrywania w trybie offline są wykrywane przez Microsoft Cloud App Security (MCAS) i wpływają na ryzyko związane z logowaniem w usłudze Identity Protection. Aby uzyskać więcej informacji na temat tych wykryć, zobacz [typy ryzyka logowania](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Istotna zmiana: fragmenty identyfikatorów URI nie będą przenoszone przy użyciu przekierowania logowania

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika
 
Od 8 lutego 2020, gdy żądanie jest wysyłane do login.microsoftonline.com w celu zalogowania użytkownika, usługa dołączy pusty fragment do żądania.  Pozwala to zapobiec atakom klasy w celu przekierowania przez zagwarantowanie, że przeglądarka wymaże wszystkie istniejące fragmenty w żądaniu. Żadna aplikacja nie powinna mieć zależności dotyczącej tego zachowania. Aby uzyskać więcej informacji, zobacz istotne [zmiany](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) w dokumentacji platformy tożsamości firmy Microsoft.

---

## <a name="december-2019"></a>Grudzień 2019 r.

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrowanie aprowizacji SAP SuccessFactors w usłudze Azure AD i lokalnej usłudze AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

Teraz możesz zintegrować rozwiązanie SAP SuccessFactors jako autorytatywne Źródło tożsamości w usłudze Azure AD. Dzięki tej integracji można zautomatyzować kompleksowy cykl życia tożsamości, w tym używanie wydarzeń opartych na usłudze HR, takich jak nowe zatrudnienie lub zakończenia, w celu kontrolowania aprowizacji kont usługi Azure AD.

Aby uzyskać więcej informacji na temat sposobu konfigurowania inicjowania obsługi przychodzącej SAP SuccessFactors w usłudze Azure AD, zobacz samouczek [Konfigurowanie funkcji automatycznej APROWIZACJI SAP SuccessFactors](https://aka.ms/SAPSuccessFactorsInboundTutorial) .

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Obsługa dostosowanych wiadomości e-mail w Azure AD B2C (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C

Możesz teraz używać Azure AD B2C do tworzenia niestandardowych wiadomości e-mail, gdy użytkownicy logują się do korzystania z aplikacji. Korzystając z usługi DisplayControls (obecnie w wersji zapoznawczej) i dostawcy poczty e-mail innej firmy (na przykład [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/)lub niestandardowego interfejsu API REST), możesz użyć własnego szablonu wiadomości e-mail, **adresu i** tekstu tematu, a także do obsługi lokalizacji i niestandardowych ustawień hasła jednorazowego (OTP).

Aby uzyskać więcej informacji, zobacz temat [niestandardowa Weryfikacja poczty e-mail w Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Zastąpienie zasad odniesienia przy użyciu domyślnych ustawień zabezpieczeń

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Bezpieczeństwo i Ochrona tożsamości

W ramach modelu bezpiecznego i domyślnego uwierzytelniania usuwamy istniejące zasady ochrony linii bazowej ze wszystkich dzierżawców. To usunięcie jest przeznaczone do ukończenia na koniec lutego. Zastąpienie tych zasad ochrony linii bazowej jest ustawieniami domyślnymi zabezpieczeń. Jeśli używasz zasad ochrony linii bazowej, musisz zaplanować przejście na nowe zasady ustawień zabezpieczeń lub dostęp warunkowy. Jeśli te zasady nie są używane, nie ma żadnych działań do wykonania.

Aby uzyskać więcej informacji na temat nowych domyślnych ustawień zabezpieczeń, zobacz [co to są ustawienia domyślne zabezpieczeń?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Aby uzyskać więcej informacji na temat zasad dostępu warunkowego, zobacz [typowe zasady dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## <a name="november-2019"></a>Listopad 2019 r.

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Obsługa atrybutu SameSite i programu Chrome 80

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

W ramach modelu bezpiecznego i domyślnego dla plików cookie Przeglądarka Chrome 80 zmienia sposób traktowania plików cookie bez atrybutu `SameSite`. Dowolny plik cookie, który nie określa atrybutu `SameSite`, będzie traktowany jak w przypadku ustawienia `SameSite=Lax`, co spowoduje zablokowanie niektórych scenariuszy udostępniania plików cookie między domenami, od których zależy aplikacja. Aby zachować starsze zachowanie programu Chrome, można użyć atrybutu `SameSite=None` i dodać dodatkowy atrybut `Secure`, dzięki czemu pliki cookie między lokacjami są dostępne tylko za pośrednictwem połączeń HTTPS. Zaplanowano ukończenie tej zmiany do 4 lutego 2020.

Zalecamy, aby wszyscy z naszych deweloperów testowali swoje aplikacje, korzystając z tych wskazówek:

- Dla ustawienia **Użyj bezpiecznego pliku cookie** ustaw wartość **tak**.

- Ustaw wartość domyślną dla atrybutu **SameSite** na **none**.

- Dodaj dodatkowy atrybut `SameSite` **bezpieczny**.

Aby uzyskać więcej informacji, zobacz [nadchodzące zmiany plików cookie SameSite w ASP.NET i ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) oraz [potencjalne zakłócenia dla witryn internetowych klienta oraz produktów i usług firmy Microsoft w programie Chrome w wersji 79 lub nowszej](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Nowa poprawka dla Microsoft Identity Manager (MIM) 2016 z dodatkiem Service Pack 2 (SP2)

**Typ:** FIXED  
**Kategoria usługi:** Microsoft Identity Manager  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

Pakiet zbiorczy poprawek (build 4.6.34.0) jest dostępny dla Microsoft Identity Manager (MIM) 2016 z dodatkiem Service Pack 2 (SP2). Ten pakiet zbiorczy rozwiązuje problemy i dodaje ulepszenia, które są opisane w sekcji "problemy rozwiązane i ulepszenia dodane w tej aktualizacji".

Aby uzyskać więcej informacji i pobrać pakiet poprawek, zobacz [pakiet zbiorczy aktualizacji pakietu Microsoft Identity Manager 2016 Service Pack 2 (kompilacja 4.6.34.0)](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Nowy raport działania aplikacji AD FS, który pomoże migrować aplikacje do usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Użyj nowego raportu aktywność aplikacji Active Directory Federation Services (AD FS) w Azure Portal, aby określić, które aplikacje mogą być migrowane do usługi Azure AD. Raport ocenia wszystkie AD FS aplikacje pod kątem zgodności z usługą Azure AD, sprawdza pod kątem problemów i zapewnia wskazówki dotyczące przygotowywania poszczególnych aplikacji do migracji.

Aby uzyskać więcej informacji, zobacz temat [Używanie raportu działania aplikacji AD FS do migrowania aplikacji do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nowy przepływ pracy do żądania zgody administratora (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Access Control

Nowy przepływ pracy zgody administratora zapewnia administratorom możliwość udzielenia dostępu do aplikacji, które wymagają zatwierdzenia przez administratora. Jeśli użytkownik próbuje uzyskać dostęp do aplikacji, ale nie może zapewnić zgody, może teraz wysłać żądanie zatwierdzenia przez administratora. Żądanie jest wysyłane pocztą e-mail i umieszczane w kolejce, która jest dostępna w Azure Portal, do wszystkich administratorów, którzy zostali wyznaczeni jako recenzenci. Gdy recenzent podejmuje działania dotyczące oczekujących żądań, żądający użytkowników są powiadamiani o tej akcji.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie przepływu pracy zgody administratora (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nowe środowisko konfiguracji aplikacja usługi Azure AD rejestracji tokenu do zarządzania opcjonalnymi oświadczeniami (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Środowisko deweloperskie

W bloku **Konfiguracja nowego tokenu aplikacja usługi Azure AD rejestracji** na Azure Portal teraz są wyświetlane aplikacje dla deweloperów aplikacji, która jest dynamiczną listą opcjonalnych oświadczeń dla aplikacji. To nowe środowisko pomaga usprawnić migracje aplikacji usługi Azure AD i zminimalizować opcjonalne konfiguracje oświadczeń.

Aby uzyskać więcej informacji, zobacz [dostarczanie opcjonalnych oświadczeń do aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nowy przepływ pracy zatwierdzania dwuetapowego w usłudze Azure AD uprawnia do zarządzania (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Zarządzanie prawami

Wprowadziliśmy Nowy dwuetapowy przepływ pracy zatwierdzania, który pozwala wymagać od dwóch osób zatwierdzających zatwierdzenie żądania użytkownika do pakietu dostępu. Na przykład można ustawić go tak, aby Menedżer żądającego użytkownika musiał najpierw ją zatwierdzić, a następnie można wymagać od właściciela zasobu zatwierdzić. Jeśli jedna z osób zatwierdzających nie zatwierdzi, dostęp nie zostanie udzielony.

Aby uzyskać więcej informacji, zobacz temat [Zmiana ustawień żądania i zatwierdzania dla pakietu dostępu w usłudze Azure AD uprawnienia zarządzania](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aktualizacje strony Moje aplikacje oraz nowe obszary robocze (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwości produktu:** integracja innej firmy

Teraz możesz dostosować sposób wyświetlania przez użytkowników w organizacji i uzyskiwania dostępu do odświeżonych funkcji Moje aplikacje. To nowe środowisko obejmuje również nowe funkcje obszarów roboczych, które ułatwiają użytkownikom znajdowanie i organizowanie aplikacji.

Aby uzyskać więcej informacji na temat nowych funkcji Moje aplikacje i tworzenia obszarów roboczych, zobacz [Tworzenie obszarów roboczych w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Obsługa identyfikatorów firmy Google Social dla współpracy B2B usługi Azure AD (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Nowe wsparcie w zakresie korzystania z identyfikatorów usługi Google Social (konta w usłudze Gmail) w usłudze Azure AD pomaga uprościć współpracę dla użytkowników i partnerów. Nie jest już konieczne, aby partnerzy mogli tworzyć nowe konta specyficzne dla firmy Microsoft i zarządzać nimi. Usługi Microsoft Teams są teraz w pełni obsługiwane przez użytkowników Google na wszystkich klientach i we wszystkich punktach końcowych uwierzytelniania związanych z dzierżawcami.

Aby uzyskać więcej informacji, zobacz temat [Dodawanie usługi Google jako dostawcy tożsamości dla użytkowników-Gości B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge mobile support dla dostępu warunkowego i logowania jednokrotnego (ogólnie dostępna)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Usługa Azure AD dla przeglądarki Microsoft Edge w systemach iOS i Android obsługuje teraz Logowanie jednokrotne usługi Azure AD i dostęp warunkowy:

- Logowanie jednokrotne **w przeglądarce Microsoft Edge:** Logowanie jednokrotne jest teraz dostępne na natywnych klientach (takich jak Microsoft Outlook i Microsoft Edge) dla wszystkich aplikacji połączonych z usługą Azure AD.

- **Dostęp warunkowy do programu Microsoft Edge:** Korzystając z zasad dostępu warunkowego opartego na aplikacji, użytkownicy muszą używać przeglądarek chronionych przez Microsoft Intune, takich jak Microsoft Edge.

Aby uzyskać więcej informacji na temat dostępu warunkowego i logowania jednokrotnego przy użyciu przeglądarki Microsoft Edge, zobacz artykuł [Obsługa urządzeń przenośnych w usłudze Microsoft Edge Mobile dla dostępu warunkowego i logowania](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) jednokrotnego. Aby uzyskać więcej informacji o sposobie konfigurowania aplikacji klienckich przy użyciu [dostępu warunkowego opartego na aplikacji](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) lub [dostępu warunkowego opartego na urządzeniach](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices), zobacz [Zarządzanie dostępem do sieci Web za pomocą przeglądarki Microsoft Intuneej chronionej przez zasady](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Zarządzanie prawami w usłudze Azure AD (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Zarządzanie prawami

Zarządzanie prawami w usłudze Azure AD to nowa funkcja zarządzania tożsamościami, która pomaga organizacjom zarządzać cyklem życia tożsamości i dostępu na dużą skalę. Ta nowa funkcja pomaga zautomatyzować przepływy pracy żądań dostępu, przypisań, przeglądów i wygaśnięcia między grupami, aplikacjami i witrynami usługi SharePoint Online.

Korzystając z usługi Azure AD uprawnia do zarządzania, możesz wydajnie zarządzać dostępem dla pracowników, a także dla użytkowników spoza organizacji, którzy potrzebują dostępu do tych zasobów.

Aby uzyskać więcej informacji, zobacz [co to jest zarządzanie prawami w usłudze Azure AD?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzowanie aprowizacji kont użytkowników dla tych nowo obsługiwanych aplikacji SaaS

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy  

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

[Usługa SAP Cloud Platform Authentication Identity](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [Priority Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzacja aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — listopad 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W listopadzie 2019 dodaliśmy następujące 21 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial) [HootSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), NegometrixPortal — Logowanie jednokrotne [(SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [Foko](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), Qmarkets [pomysł & Zarządzanie innowacje](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), Netskopee [uwierzytelnianie użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) , [JISC student głosujących Registration](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nowa i ulepszona Galeria aplikacji usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Zaktualizowaliśmy galerię aplikacji usługi Azure AD, aby ułatwić znajdowanie wstępnie zintegrowanych aplikacji obsługujących obsługę administracyjną, OpenID Connect Connect i SAML w dzierżawie Azure Active Directory.

Aby uzyskać więcej informacji, zobacz [Dodawanie aplikacji do dzierżawy Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Zwiększono limit długości definicji roli aplikacji z 120 do 240 znaków

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Firma Microsoft wysłuchuje od klientów, że długość limitu dla wartości definicji roli aplikacji w niektórych aplikacjach i usługach jest za mała o 120 znaków. W odpowiedzi Zwiększono maksymalną długość definicji wartości roli do 240 znaków.

Aby uzyskać więcej informacji o korzystaniu z definicji ról specyficznych dla aplikacji, zobacz [Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---

## <a name="october-2019"></a>Październik 2019 r.

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Wycofanie interfejsu API identityRiskEvent na potrzeby wykrywania ryzyka Azure AD Identity Protection  

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

W odpowiedzi na opinie dla deweloperów Azure AD — wersja Premium w przypadku subskrybentów P2 mogą teraz wykonywać złożone zapytania dotyczące danych wykrywania ryzyka Azure AD Identity Protection przy użyciu nowego interfejsu API riskDetection na potrzeby Microsoft Graph. Istniejąca wersja beta interfejsu API [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) przestanie zwracać dane **na około 10 stycznia 2020**. Jeśli Twoja organizacja korzysta z interfejsu API identityRiskEvent, należy przejść do nowego interfejsu API riskDetection.

Więcej informacji na temat nowego interfejsu API riskDetection można znaleźć w [dokumentacji dotyczącej interfejsu API wykrywania ryzyka](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Obsługa atrybutu SameSite i programu Chrome 80 przez serwer proxy aplikacji

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Kilka tygodni przed publikacją w przeglądarce Chrome 80, firma Microsoft planuje aktualizację sposobu, w jaki pliki cookie serwera proxy aplikacji traktują atrybut **SameSite** . W wersji programu Chrome 80 każdy plik cookie, który nie określa atrybutu **SameSite** , będzie traktowany, jakby był ustawiony na `SameSite=Lax`.

Aby uniknąć potencjalnie ujemnych wpływów spowodowanych tą zmianą, aktualizujemy dostęp do serwera proxy aplikacji i plików cookie sesji przez:

- Ustawienie wartości domyślnej dla ustawienia **Użyj bezpiecznego pliku cookie** na wartość **tak**.

- Ustawianie wartości domyślnej dla atrybutu **SameSite** na **none**.

    >[!NOTE]
    > Pliki cookie dostępu do serwera proxy aplikacji są zawsze przesyłane wyłącznie za pośrednictwem bezpiecznych kanałów. Te zmiany dotyczą tylko plików cookie sesji.

Aby uzyskać więcej informacji na temat ustawień plików cookie serwera proxy aplikacji, zobacz [Ustawienia plików cookie dotyczące uzyskiwania dostępu do aplikacji lokalnych w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Rejestracje aplikacji (starsza wersja) i zbieżność zarządzania aplikacjami z portalu rejestracji aplikacji (apps.dev.microsoft.com) nie będą już dostępne

**Typ:** Planowanie zmiany  
**Kategoria usługi:** NIE DOTYCZY  
**Możliwość produktu:** Środowisko deweloperskie

W najbliższej przyszłości użytkownicy z kontami usługi Azure AD nie będą już mogli rejestrować aplikacji zbieżnych i zarządzać nimi za pomocą portalu rejestracji aplikacji (apps.dev.microsoft.com) ani rejestrować aplikacji i zarządzać nimi w Rejestracje aplikacji (starsza wersja) środowisko pracy w Azure Portal.

Aby dowiedzieć się więcej na temat nowego środowiska Rejestracje aplikacji, zobacz [rejestracje aplikacji w przewodniku szkolenia Azure Portal](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Użytkownicy nie muszą już być ponownie rejestrowani podczas migracji z usługi MFA dla poszczególnych użytkowników do usługi MFA opartej na dostępie warunkowym

**Typ:** FIXED  
**Kategoria usługi:** Funkcja  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Rozwiązano znany problem polegający na tym, że użytkownicy musieli ponownie zarejestrować się, jeśli zostały wyłączone dla Multi-Factor Authentication poszczególnych użytkowników (MFA), a następnie włączono usługę MFA za pomocą zasad dostępu warunkowego.

Aby wymagać od użytkowników ponownego zarejestrowania, można wybrać opcję **wymagana ponowna rejestracja usługi MFA** z metod uwierzytelniania użytkownika w portalu usługi Azure AD. Aby uzyskać więcej informacji na temat migrowania użytkowników z usługi MFA do usługi MFA na podstawie dostępu warunkowego, zobacz [konwertowanie użytkowników z usługi MFA na użytkownika na podstawie dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nowe możliwości przekształcania i wysyłania oświadczeń w tokenie SAML

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** ZWRÓCIŁ

Dodaliśmy dodatkowe możliwości, które ułatwiają Dostosowywanie i wysyłanie oświadczeń w tokenie SAML. Te nowe możliwości obejmują:

- Dodatkowe funkcje przekształcania oświadczeń, które ułatwiają modyfikowanie wartości wysyłanej w ramach oświadczenia.

- Możliwość zastosowania wielu przekształceń do pojedynczego żądania.

- Możliwość określenia źródła roszczeń w oparciu o typ użytkownika i grupę, do której należy użytkownik.

Aby uzyskać szczegółowe informacje na temat tych nowych funkcji, w tym ich używania, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nowa strona logowania dla użytkowników końcowych w usłudze Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Monitorowanie & raportowania

Dodaliśmy nową stronę **Moje logowania** (https://mysignins.microsoft.com), aby umożliwić użytkownikom w organizacji wyświetlanie ich ostatnich historii logowania w celu sprawdzenia, czy nie występują żadne nietypowe działania. Ta nowa strona umożliwia użytkownikom wyświetlanie:

- Jeśli ktoś podejmie próbę odgadnięcia hasła.

- Jeśli osoba atakująca pomyślnie zalogował się na swoje konto i z lokalizacji.

- Jakie aplikacje próbowały uzyskać dostęp osoby atakującej.

Aby uzyskać więcej informacji, zobacz, czy [Użytkownicy mogą teraz sprawdzić historię logowania w blogu dotyczącym nietypowej aktywności](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migracja Azure AD Domain Services (Azure AD DS) z wersji klasycznej do Azure Resource Manager sieci wirtualnych

**Typ:** Nowa funkcja  
**Kategoria usługi:** Azure AD Domain Services  
**Możliwość produktu:** Azure AD Domain Services

Naszym klientom, którzy mieli zablokowaną obsługę klasycznych sieci wirtualnych — mamy wspaniałe wiadomości! Teraz można przeprowadzić jednorazową migrację z klasycznej sieci wirtualnej do istniejącej Menedżer zasobów sieci wirtualnej. Po przejściu do sieci wirtualnej Menedżer zasobów będzie można korzystać z dodatkowych i uaktualnionych funkcji, takich jak szczegółowe zasady haseł, powiadomienia e-mail i dzienniki inspekcji.

Aby uzyskać więcej informacji, zobacz [wersja zapoznawcza — migruj Azure AD Domain Services z klasycznego modelu sieci wirtualnej do Menedżer zasobów](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Aktualizacje układu strony Azure AD B2C

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C

Wprowadzono nowe zmiany w wersji 1.2.0 kontraktu dotyczącego strony dla Azure AD B2C. W tej zaktualizowanej wersji można teraz sterować kolejnością obciążeń dla elementów, co może również pomóc w zatrzymaniu migotania wykonywanego po załadowaniu arkusza stylów (CSS).

Aby uzyskać pełną listę zmian wprowadzonych w umowie dotyczącej strony, zobacz [Dziennik zmian wersji](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aktualizowanie na stronie Moje aplikacje wraz z nowymi obszarami roboczymi (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwość produktu:** Access Control

Teraz możesz dostosować sposób, w jaki użytkownicy organizacji wyświetlają i uzyskują dostęp do nowego środowiska moje aplikacje, w tym za pomocą funkcji nowe obszary robocze, aby ułatwić im znajdowanie aplikacji. Nowe funkcje obszarów roboczych działają jako filtr dla aplikacji, do których użytkownicy w organizacji mają już dostęp.

Aby uzyskać więcej informacji na temat wdrażania nowych funkcji Moje aplikacje i tworzenia obszarów roboczych, zobacz [Tworzenie obszarów roboczych w portalu My Apps (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Obsługa miesięcznego aktywnego modelu rozliczania opartego na użytkownikach (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C — Zarządzanie tożsamościami konsumentów  
**Możliwość produktu:** B2B/B2C

Azure AD B2C teraz obsługuje rozliczenia comiesięcznych aktywnych użytkowników (MAU). Rozliczenia MAU są oparte na liczbie unikatowych użytkowników z aktywnością uwierzytelniania w miesiącu kalendarzowym. Istniejący klienci mogą w dowolnym momencie przełączyć się do tej nowej metody rozliczania.

Od 1 listopada 2019 Wszyscy nowi klienci będą automatycznie rozliczani przy użyciu tej metody. Ta metoda rozliczania przynosi klientom korzyści wynikające z kosztów i możliwości planowania.

Aby uzyskać więcej informacji, zobacz [uaktualnianie do miesięcznego modelu rozliczeń aktywnych użytkowników](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — październik 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W październiku 2019 dodaliśmy do galerii aplikacji 35 te nowe aplikacje z obsługą Federacji:

[W przypadku kryzysu — Mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [nienaruszone](https://tact.ai/assistant/) [OpusCapita Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [dynaTrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [Content](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue koordynuje — UE](https://www.hirevue.com/), [HireVue koordynuje-USOnly](https://www.hirevue.com/), [HireVue koordynuje US](https://www.hirevue.com/), [WittyParrot Pole merytoryczne](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), Emailing [!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Teams](https://theteamie.com/), [prędkość dla zespołów](https://velocity.peakup.org/teams/login), [aplikacji signl4](https://account.signl4.com/manage), [EAB](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), Impl, [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), [poczta e-mail dla usługi Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [poczta e-mail dla pakietu Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct) [,](https://ihealthnav.com/account/signin) [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial) [ Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Skonsolidowany element menu zabezpieczeń w portalu usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Teraz możesz uzyskać dostęp do wszystkich dostępnych funkcji zabezpieczeń usługi Azure AD z poziomu nowego elementu menu **zabezpieczenia** i z paska **wyszukiwania** w Azure Portal. Ponadto nowa strona docelowa **zabezpieczeń** , nazywana **zabezpieczeniami — wprowadzenie**, oferuje linki do naszej dokumentacji publicznej, wskazówek dotyczących zabezpieczeń i przewodników wdrażania.

Nowe menu **zabezpieczeń** zawiera:

- Dostęp warunkowy
- Identity Protection
- Centrum zabezpieczeń
- Ocena bezpiecznego tożsamości
- Metody uwierzytelniania
- Funkcja
- Raporty o podwyższonym ryzyku — Ryzykowni użytkownicy, ryzykowne logowania, wykrywanie ryzyka
- i więcej...

Aby uzyskać więcej informacji, zobacz [zabezpieczenia — wprowadzenie](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Zasady wygasania grup pakietu Office 365 z funkcją autoodnawiania

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

Zasady wygasania grup pakietu Office 365 zostały udoskonalone w celu automatycznego odnawiania grup, które są aktywnie używane przez jego członków. Grupy będą autoodnawiane na podstawie aktywności użytkownika we wszystkich aplikacjach pakietu Office 365, w tym programów Outlook, SharePoint i Teams.

To ulepszenie pomaga zmniejszyć liczbę powiadomień o wygasaniu grup i pomaga upewnić się, że aktywne grupy są nadal dostępne. Jeśli masz już aktywne zasady wygasania dla grup programu Office 365, nie musisz nic robić, aby włączyć tę nową funkcję.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad wygasania dla grup pakietu Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Zaktualizowano środowisko tworzenia Azure AD Domain Services (Azure AD DS)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Azure AD Domain Services  
**Możliwość produktu:** Azure AD Domain Services

Zaktualizowaliśmy Azure AD Domain Services (Azure AD DS), aby uwzględnić nowe i udoskonalone środowisko tworzenia, co pomoże Ci utworzyć domenę zarządzaną w zaledwie trzech kliknięciach. Ponadto możesz teraz przekazywać i wdrażać AD DS platformy Azure z szablonu.

Aby uzyskać więcej informacji, zobacz [Samouczek: Tworzenie i Konfigurowanie wystąpienia Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---

## <a name="september-2019"></a>Wrzesień 2019 r.

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Zaplanuj zmianę: zaniechanie Power BI pakietów zawartości

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Od 1 października 2019 Power BI rozpocznie się wycofanie wszystkich pakietów zawartości, w tym pakietu zawartości usługi Azure AD Power BI. Alternatywą dla tego pakietu zawartości jest użycie skoroszytów usługi Azure AD w celu uzyskania szczegółowych informacji dotyczących usług związanych z usługą Azure AD. Dostępne są dodatkowe skoroszyty, w tym skoroszyty dotyczące zasad dostępu warunkowego w trybie tylko raportowanie, informacje oparte na zgodzie aplikacji i inne.

Aby uzyskać więcej informacji na temat skoroszytów, zobacz [jak używać skoroszytów Azure monitor dla Azure Active Directory raportów](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Aby uzyskać więcej informacji na temat wycofania pakietów zawartości, zobacz wpis w blogu dotyczący [ogłaszania Power BI szablonów aplikacji ogólnie dostępna](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Mój profil zmienia nazwę i integruje się ze stroną konta Microsoft Office

**Typ:** Planowanie zmiany  
**Kategoria usługi:** Mój profil/konto  
**Możliwość produktu:** Społeczności

Od października środowisko my profilu zostanie moje konto. W ramach tej zmiany wszędzie tam, gdzie się znajduje, **mój profil** zmieni się na **Moje konto**. W oparciu o zmiany nazw i niektóre ulepszenia projektowe zaktualizowane środowisko oferuje dodatkową integrację ze stroną konta Microsoft Office. W związku z tym będziesz mieć dostęp do instalacji i subskrypcji pakietu Office na stronie z **omówieniem konta** oraz z preferencjami kontaktu związanymi z pakietem Office na stronie **prywatność** .

Aby uzyskać więcej informacji na temat środowiska my profile (wersja zapoznawcza), zobacz temat [Omówienie portalu My profil (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Zbiorcze zarządzanie grupami i członkami przy użyciu plików CSV w portalu usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Z przyjemnością ogłaszamy publiczną wersję zapoznawczą środowiska zarządzania grupami zbiorczymi w portalu usługi Azure AD. Można teraz używać pliku CSV i portalu usługi Azure AD do zarządzania grupami i listami elementów członkowskich, w tym:

- Dodawanie członków do grupy lub usuwanie ich z niej.

- Pobieranie listy grup z katalogu.

- Pobieranie listy członków grupy dla określonej grupy.

Aby uzyskać więcej informacji, zobacz [zbiorcze Dodawanie członków](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [zbiorcze usuwanie](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)członków, [listy członków pobierania zbiorczego](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)i [listę grup pobierania zbiorczego](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Zgoda dynamiczna jest teraz obsługiwana za pomocą nowego punktu końcowego zgody na administratora

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Utworzyliśmy nowy punkt końcowy zgody administratora, który będzie obsługiwał zgodę dynamiczną, co jest przydatne w przypadku aplikacji, które chcą korzystać z modelu zgody dynamicznej na platformie tożsamości firmy Microsoft.

Aby uzyskać więcej informacji o sposobach korzystania z tego nowego punktu końcowego, zobacz [Korzystanie z punktu końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — wrzesień 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

We wrześniu 2019 dodaliśmy te 29 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [Usługa MS Azure SSO dostęp do usługi Ethidex w pakiecie Office™ — logowanie](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial)jednokrotne, [Portal IServer](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [Concur podróży i wydatków](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), [YeeFlow](https://apps.yeeflow.com/), [centra](https://clients.jdlt.co.uk/login) [Arc](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [Luware Stratus Team](https://stratus.emea.luware.cloud/login), [](https://app.penneo.com/) [szerokie pomysły](https://wideideas.online/wideideas/), [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial) [JDLT,](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive) [Renraku](https://app.testhtm.com/settings/email-integration) [, SealPath, Prisma](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial) [](https://aec.cintoo.com/login) [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [hostowana w trybie online Rejestracja jednokrotna](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [bis](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [dyrektor, Kompilacja zespołu](https://ms-contacts.coo-kai.jp/), [SonarQube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), [odnajdywanie korzyści z rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/), [ITask](https://itask.yipinapp.com/)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-global-reader-role"></a>Nowa rola czytnika globalnego usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** RBAC  
**Możliwość produktu:** Access Control

Od 24 września 2019 zostanie rozpoczęta nowa rola Azure Active Directory (AD) o nazwie Reader Global. To wdrożenie rozpocznie się wraz z produkcyjnym i globalnym klientom w chmurze (w zatoce), kończąc na całym świecie w październiku.

Globalna rola czytnika to odpowiedni dla administratora globalnego tylko do odczytu. Użytkownicy w tej roli mogą odczytywać ustawienia i informacje administracyjne w ramach usług Microsoft 365, ale nie mogą podejmować działań związanych z zarządzaniem. Utworzyliśmy globalną rolę czytelnika, która pomaga w zmniejszeniu liczby administratorów globalnych w organizacji. Ponieważ konta administratorów globalnych są zaawansowane i podatne na ataki, zalecamy użycie mniej niż pięciu administratorów globalnych. Zalecamy korzystanie z roli czytnika globalnego na potrzeby planowania, inspekcji lub badań. Zalecamy również korzystanie z roli czytnika globalnego w połączeniu z innymi ograniczonymi rolami administratora, takimi jak administrator programu Exchange, aby ułatwić wykonanie pracy bez konieczności posiadania roli administratora globalnego.

Globalna rola czytnika współpracuje z nowym centrum administracyjnym Microsoft 365, centrum administracyjnym programu Exchange, centrum administracyjnym zespołów, Security Center, centrum zgodności, centrum administracyjnym usługi Azure AD oraz centrum administracyjnym zarządzania urządzeniami.

>[!NOTE]
> Na początku publicznej wersji zapoznawczej rola czytnika globalnego nie będzie współpracująca z: SharePoint, Privileged Access Management, Skrytka klienta, etykietami czułości, cyklem życia zespołów, raportowaniem & wywołań analizy, zespoły IP zarządzanie urządzeniami telefonicznymi i zespołami Pełnotekstowy. Wszystkie te usługi są przeznaczone do pracy z rolą w przyszłości.

Aby uzyskać więcej informacji, zobacz [uprawnienia roli administrator w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Uzyskiwanie dostępu do lokalnego serwera raportów z aplikacji mobilnej Power BI przy użyciu serwer proxy aplikacji usługi Azure Active Directory

**Typ:** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Nowa integracja między aplikacją mobilną Power BI a usługą Azure serwer proxy aplikacji usługi Azure AD umożliwia bezpieczne logowanie do aplikacji mobilnej Power BI i wyświetlanie dowolnych raportów organizacji hostowanych w Serwer raportów usługi Power BI lokalnym.

Aby uzyskać informacje o aplikacji mobilnej Power BI, w tym o lokalizacji, w której należy pobrać aplikację, zapoznaj się z [witryną Power BI](https://powerbi.microsoft.com/mobile/). Aby uzyskać więcej informacji na temat sposobu konfigurowania aplikacji mobilnej Power BI przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD, zobacz [Włączanie dostępu zdalnego do Power BI Mobile przy użyciu usługi azure serwer proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Dostępna jest nowa wersja modułu AzureADPreview PowerShell

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Katalogi

Do modułu AzureADPreview dodano nowe polecenia cmdlet, które ułatwiają Definiowanie i przypisywanie ról niestandardowych w usłudze Azure AD, w tym:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nowa wersja Azure AD Connect

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Katalogi

Firma Microsoft udostępniła zaktualizowaną wersję Azure AD Connect dla klientów korzystających z aktualizacji. Ta nowa wersja zawiera kilka nowych funkcji, ulepszeń i poprawek błędów. Aby uzyskać więcej informacji o tej nowej wersji, zobacz [Azure AD Connect: historia](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250)wersji.

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Serwer usługi Azure Multi-Factor Authentication (MFA) w wersji 8.0.2 jest teraz dostępny

**Typ:** FIXED  
**Kategoria usługi:** Funkcja  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Jeśli jesteś istniejącym klientem, który aktywuje serwer usługi MFA przed 1 lipca 2019, możesz teraz pobrać najnowszą wersję serwera usługi MFA (wersja 8.0.2). W tej nowej wersji:

- Rozwiązano problem polegający na tym, że gdy usługa Azure AD Sync zmieni użytkownika z wyłączone na włączone, do użytkownika zostanie wysłana wiadomość e-mail.

- Rozwiązano problem, aby klienci mogli pomyślnie uaktualnić program, jednocześnie używając funkcji tagów.

- Dodano kod kraju Kosowa (+ 383).

- Dodano jednorazowe rejestrowanie inspekcji do dziennika MultiFactorAuthSvc. log.

- Ulepszona wydajność zestawu SDK usługi sieci Web.

- Rozwiązano inne drobne błędy.

Od 1 lipca 2019 firma Microsoft zatrzymała ofertę serwera usługi MFA dla nowych wdrożeń. Nowi klienci, którzy wymagają uwierzytelniania wieloskładnikowego, powinni korzystać z usługi Azure Multi-Factor Authentication opartej na chmurze. Aby uzyskać więcej informacji, zobacz [Planowanie wdrożenia usługi Azure Multi-Factor Authentication opartej na chmurze](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---
