---
title: Co nowego? Informacje o wersji — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się, co nowego w usłudze Azure Active Directory, takie jak najnowsze informacje o wersji, znane problemy, poprawki błędów, przestarzałe funkcje i nadchodzące zmiany.
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
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 491908b651cd2b875fcfeed4c55d34f0e8c5cfa1
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802513"
---
# <a name="whats-new-in-azure-active-directory"></a>Co nowego w usłudze Azure Active Directory?

>Otrzymuj powiadomienia o tym, kiedy można ponownie odwiedzić `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` tę ![stronę w poszukiwaniu](./media/whats-new/feed-icon-16x16.png) aktualizacji, kopiując i wklejając ten adres URL: do czytnika kanałów informacyjnych czytnika kanałów informacyjnych RSS.

Usługa Azure AD otrzymuje ulepszenia na bieżąco. Aby być na bieżąco z najnowszymi wydarzeniami, w tym artykule znajdziesz informacje na temat:

- Najnowsze wydania
- Znane problemy
- Poprawki błędów
- Funkcje uznane za przestarzałe
- Plany zmian

Ta strona jest aktualizowana co miesiąc, więc regularnie ją odwiedzaj. Jeśli szukasz elementów starszych niż sześć miesięcy, możesz je znaleźć w [archiwum dla nowości w usłudze Azure Active Directory](whats-new-archive.md).

---

## <a name="march-2020"></a>Marzec 2020 r.

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Niezarządzane konta usługi Azure Active Directory w aktualizacji B2B na marzec 2021 r.

**Typ:** Plan zmian  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 
**Począwszy od 31 marca 2021**r. firma Microsoft nie będzie już obsługiwać realizacji zaproszeń, tworząc niezarządzane konta usługi Azure Active Directory (Azure AD) i dzierżawy dla scenariuszy współpracy B2B. W ramach przygotowań do tego, zachęcamy do wyrażenia zgody na [uwierzytelnianie jednorazowym kodem dostępu.](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Użytkownicy z domyślną rolą dostępu będą mogli aprowizacji

**Typ:** Plan zmian  
**Kategoria usługi:** Inicjowanie obsługi administracyjnej aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
W przeszłości użytkownicy z domyślną rolą dostępu byli poza zakresem inicjowania obsługi administracyjnej. Słyszeliśmy opinie, że klienci chcą, aby użytkownicy z tą rolą byli w zakresie inicjowania obsługi administracyjnej. Pracujemy nad wdrożeniem zmiany, dzięki czemu wszystkie nowe konfiguracje inicjowania obsługi administracyjnej umożliwią użytkownikom z domyślną rolą dostępu aprowizowanie. Stopniowo będziemy zmieniać zachowanie dla istniejących konfiguracji inicjowania obsługi administracyjnej użytkowników z tej roli. Nie jest wymagane żadne działanie klienta. Po wprowadzeniu tej zmiany opublikujemy aktualizację naszej [dokumentacji.](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Współpraca usługi Azure AD B2B będzie dostępna na platformie Microsoft Azure obsługiwanej przez dzierżawę 21Vianet (Azure China 21Vianet)

**Typ:** Plan zmian  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 
Możliwości współpracy usługi Azure AD B2B zostaną udostępnione na platformie Microsoft Azure obsługiwanej przez dzierżawę 21Vianet (Azure China 21Vianet), umożliwiając użytkownikom w dzierżawie usługi Azure China 21Vianet bezproblemową współpracę z użytkownikami w innych dzierżawach usługi Azure China 21Vianet. [Dowiedz się więcej o współpracy usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Przeprojektowanie wiadomości e-mail z zaproszeniem do współpracy usługi Azure AD B2B

**Typ:** Plan zmian  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 
Wiadomości [e-mail](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) wysyłane przez usługę zaproszenie do współpracy usługi Azure AD B2B w celu zaproszenia użytkowników do katalogu zostaną przeprojektowane, aby informacje o zaproszeniu i następne kroki użytkownika były jaśniejsze.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>HomeRealmSekcje zasad wykrywania pojawią się w dziennikach inspekcji

**Typ:** Stałe  
**Kategoria usługi:** Inspekcji  
**Możliwości produktu:** Monitorowanie & raportowania
 
Naprawiliśmy błąd, w którym zmiany [zasad HomeRealmDiscovery](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) nie zostały uwzględnione w dziennikach inspekcji. Teraz będzie można zobaczyć, kiedy i jak polityka została zmieniona, i przez kogo. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — marzec 2020 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich
 
W marcu 2020 r. dodaliśmy do galerii aplikacji 51 nowych aplikacji z obsługą federacji: 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Zoho One China](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co SAML App](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial), [iPoint Service Provider](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial), contexxt.ai [SPHERE](https://contexxt-sphere.com/login), [Mądrość Przez Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial), [Flare Digital Signage](https://spark-dev.pixelnebula.com/login), [Logz.io - Cloud Observability dla inżynierów](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial), [SpectrumU](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial), [BizzContact](https://bizzcontact.app/), [Elqano SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial), [MarketSignShare](http://www.signshare.com/), [CrossKnowledge Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial), [Netvision Compas](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial), [FCM HUB](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial), RIB A / S [Byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757), [GoLinks](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial), [Datadog](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial), [Zscaler B2B User Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial), [LIFT](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial), [Planview Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial), [WatchTeams](https://www.devfinition.com/) [, Aster](https://demo.asterapp.io/login), [Umiejętności Przepływ pracy](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial), [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx), [Platforma IP](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial), [InVision](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial), [Pipedrive](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial), [Showcase Workshop](https://app.showcaseworkshop.com/), [Greenlight Integration Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial), [Greenlight Zgodne Zarządzanie dostępem](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial), [Grok Learning](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial), [Miradore Online](https://login.online.miradore.com/), [Khoros Care](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial), [AskYourTeam](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial), [TruNarrative](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial), [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [Bi Studio dla digital process automation](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial), [insuiteX](https://www.insuite.jp/), [sybo](https://www.systexsoftware.com.tw/), [Britive](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial), [WhosOffice](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial), [E-dni](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial), [Kollective SDN](https://portal.kollective.app/login), [Witivio](https://app.witivio.com/), [Playvox](https://my.playvox.com/login), [Korn Ferry 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial), [Campus Café](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial), [Catchpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial), [Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Współpraca usługi Azure AD B2B dostępna w dzierżawach platformy Azure dla instytucji rządowych

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** B2B/B2C
 
Funkcje współpracy usługi Azure AD B2B są teraz dostępne między niektórymi dzierżawami platformy Azure dla instytucji rządowych.  Aby dowiedzieć się, czy dzierżawa może korzystać z tych funkcji, postępuj zgodnie z instrukcjami w [jak sprawdzić, czy współpraca B2B jest dostępna w dzierżawie usługi Azure US Government?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Integracja z usługą Azure Monitor dla dzienników platformy Azure jest teraz dostępna w usłudze Azure Dla Instytucji rządowych

**Typ:** Nowa funkcja  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania
 
Integracja usługi Azure Monitor z dziennikami usługi Azure AD jest teraz dostępna w usłudze Azure Dla Instytucji. Dzienniki usługi Azure AD (dzienniki inspekcji i logowania) można rozsyłać do konta magazynu, usługi Event Hub i usługi Log Analytics. Zapoznaj się ze [szczegółową dokumentacją,](https://aka.ms/aadlogsinamd) a także [planami wdrażania dla raportowania i monitorowania](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) scenariuszy usługi Azure AD.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Odświeżanie ochrony tożsamości w usłudze Azure Dla instytucji rządowych

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Z przyjemnością udostępnimy, że teraz wdrożyliśmy odświeżone środowisko [usługi Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs) w [portalu Microsoft Azure Dla Instytucji Rządowych.](https://portal.azure.us/) Aby uzyskać więcej informacji, zobacz nasz [wpis na blogu ogłoszenia](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Odzyskiwanie po awarii: pobieranie i przechowywanie konfiguracji inicjowania obsługi administracyjnej

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi administracyjnej aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości
 
Usługa inicjowania obsługi administracyjnej usługi Azure AD zapewnia bogaty zestaw możliwości konfiguracji. Klienci muszą mieć możliwość zapisania swojej konfiguracji, aby mogli odwołać się do niej później lub przywrócić do znanej dobrej wersji. Dodaliśmy możliwość pobrania konfiguracji inicjowania obsługi administracyjnej jako pliku JSON i przesłania jej, gdy jest potrzebna. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (samoobsługowe resetowanie hasła) wymaga teraz dwóch bramek dla administratorów platformy Microsoft Azure obsługiwanych przez firmę 21Vianet (Azure China 21Vianet) 

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości
 
Wcześniej na platformie Microsoft Azure obsługiwanej przez firmę 21Vianet (Azure China 21Vianet) administratorzy korzystający z samoobsługowego resetowania haseł (SSPR) do resetowania własnych haseł potrzebowali tylko jednej "bramy" (wyzwania) do udowodnienia swojej tożsamości. W chmurach publicznych i innych chmur krajowych administratorzy zazwyczaj muszą używać dwóch bramek, aby udowodnić swoją tożsamość podczas korzystania z sspr. Ale ponieważ nie obsługujemy połączeń SMS lub telefonicznych w usłudze Azure China 21Vianet, zezwoliliśmy na resetowanie hasła z jedną bramką przez administratorów.

Tworzymy parzystość funkcji SSPR między usługą Azure China 21Vianet a chmurą publiczną. Idąc dalej, administratorzy muszą używać dwóch bramek podczas korzystania z funkcji SSPR. Obsługiwane będą powiadomienia i kody sms, połączeń telefonicznych i aplikacji Authenticator. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>Długość hasła jest ograniczona do 256 znaków

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Aby zapewnić niezawodność usługi Azure AD, hasła użytkowników są teraz ograniczone do 256 znaków. Użytkownicy z hasłami dłuższymi niż to zostaną poproszeni o zmianę hasła przy kolejnym logowaniu, kontaktując się z administratorem lub korzystając z funkcji samoobsługowego resetowania hasła.

Ta zmiana została włączona 13 marca 2020 r. o godzinie 10:00 czasu pst (18:00 UTC), a błąd to AADSTS 50052, InvalidPasswordExceedsMaxLength. Zobacz [powiadomienie o zmianie podziału,](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) aby uzyskać więcej informacji.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Dzienniki logowania usługi Azure AD są teraz dostępne dla wszystkich bezpłatnych dzierżaw za pośrednictwem witryny Azure portal

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwości produktu:** Monitorowanie & raportowania
 
Od teraz klienci, którzy mają bezpłatnych dzierżaw, mogą uzyskać dostęp do [dzienników logowania usługi Azure AD z witryny Azure portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) przez maksymalnie 7 dni. Wcześniej dzienniki logowania były dostępne tylko dla klientów posiadających licencje usługi Azure Active Directory Premium. Dzięki tej zmianie wszyscy dzierżawcy mogą uzyskiwać dostęp do tych dzienników za pośrednictwem portalu.

> [!NOTE]
> Klienci nadal potrzebują licencji premium (Azure Active Directory Premium P1 lub P2), aby uzyskać dostęp do dzienników logowania za pośrednictwem interfejsu API programu Microsoft Graph i usługi Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Deprecation of Directory-wide groups option from Groups General Settings on Azure portal Deprecation of Directory-wide groups groups

**Typ:** Przestarzałe  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Współpracy

Aby zapewnić klientom bardziej elastyczny sposób tworzenia grup dla całego katalogu, które najlepiej odpowiadają ich potrzebom, zastąpiliśmy opcję **Grupy dla całego katalogu** w **ustawieniach ogólne grup** > **General** w witrynie Azure portal łączem do [dynamicznej dokumentacji grupy.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) Ulepszyliśmy naszą dokumentację, aby zawierała więcej instrukcji, aby administratorzy mogli tworzyć grupy wszystkich użytkowników, które zawierają lub wykluczają użytkowników-gości.

---

## <a name="february-2020"></a>Luty 2020 r.

### <a name="upcoming-changes-to-custom-controls"></a>Nadchodzące zmiany w formantach niestandardowych

**Typ:** Plan zmian  
**Kategoria usługi:** Mfa  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości
 
Planujemy zastąpić bieżącą wersję zapoznawczą formantów niestandardowych podejściem, które umożliwia bezproblemową pracę z administratorem usługi Azure Active Directory i użytkownikami końcowymi. Obecnie rozwiązania programu PARTNER MFA napotykają następujące ograniczenia: działają dopiero po wprowadzeniu hasła; nie służą one jako uwierzytelnianie wieloskładnikowe do intensyfikowania uwierzytelniania w innych kluczowych scenariuszach; i nie integrują się z funkcjami zarządzania poświadczeniami użytkownika końcowego lub administracyjnego. Nowa implementacja umożliwi agentom uwierzytelniania dostarczonym przez partnera pracę z wbudowanymi czynnikami dla kluczowych scenariuszy, w tym rejestracją, użyciem, oświadczeniami usługi MFA, intensyfikacją uwierzytelniania, raportowaniem i rejestrowaniem. 

Formanty niestandardowe będą nadal obsługiwane w wersji zapoznawczej wraz z nowym projektem, dopóki nie osiągnie ogólnej dostępności. W tym momencie damy klientom czas na migrację do nowego projektu. Ze względu na ograniczenia obecnego podejścia nie będziemy dołączać do nowych dostawców, dopóki nowy projekt nie będzie dostępny. Ściśle współpracujemy z klientami i dostawcami i komunikujemy harmonogram, gdy się zbliżamy. [Dowiedz się więcej](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identity Secure Score - Aktualizacje akcji poprawy usługi MFA

**Typ:** Plan zmian  
**Kategoria usługi:** Mfa  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości
 
Aby odzwierciedlić potrzebę zapewnienia przez firmy najwyższego bezpieczeństwa podczas stosowania zasad, które współpracują z ich działalnością, microsoft Secure Score usuwa trzy akcje udoskonalania skoncentrowane wokół uwierzytelniania wieloskładnikowego (MFA) i dodaje dwa.

Zostaną usunięte następujące działania udoskonalania:

- Zarejestruj wszystkich użytkowników w celu uzyskania usługi MFA
- Wymaganie uwierzytelniania wieloskładnikowego dla wszystkich użytkowników
- Wymagaj usługi MFA dla ról uprzywilejowanych usługi Azure AD

Zostaną dodane następujące działania udoskonalania:

- Upewnij się, że wszyscy użytkownicy mogą ukończyć uwierzytelnianie wieloskładnikowe w celu zapewnienia bezpiecznego dostępu
- Wymagaj usługi MFA dla ról administracyjnych

Te nowe akcje poprawy będą wymagać rejestrowania użytkowników lub administratorów usługi MFA w katalogu i ustanawiania odpowiedniego zestawu zasad, które odpowiadają twoim potrzebom organizacyjnym. Głównym celem jest zapewnienie elastyczności przy jednoczesnym zapewnieniu wszystkim użytkownikom i administratorom możliwości uwierzytelniania przy użyciu wielu czynników lub monitów weryfikacji tożsamości opartych na ryzyku. Może to przybrać formę ustawiania domyślnych zabezpieczeń, które pozwalają firmie Microsoft zdecydować, kiedy zakwestionować użytkowników usługi MFA lub o wiele zasad, które stosują decyzje o określonym zakresie. W ramach tych aktualizacji akcji poprawy zasady ochrony według planu bazowego nie będą już uwzględniane w obliczeniach oceniania. [Dowiedz się więcej o tym, co pojawi się w programie Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Wybór jednostki SKU usług domenowych usługi Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Usługi domenowe usługi Azure AD  
**Możliwości produktu:** Usługi domenowe usługi Azure AD
 
Słyszeliśmy opinie, że klienci usług domenowych usługi AZURE AD chcą większej elastyczności w wyborze poziomów wydajności dla swoich wystąpień. Począwszy od 1 lutego 2020 r. przełączyliśmy się z modelu dynamicznego (w którym usługa Azure AD określa wydajność i warstwę cenową na podstawie liczby obiektów) na model samowyborczy. Teraz klienci mogą wybrać warstwę wydajności, która pasuje do ich środowiska. Ta zmiana pozwala nam również włączyć nowe scenariusze, takie jak Lasy zasobów i funkcje Premium, takie jak codzienne kopie zapasowe. Liczba obiektów jest teraz nieograniczona dla wszystkich jednostek SKU, ale nadal będziemy oferować sugestie dotyczące liczby obiektów dla każdej warstwy.

**Nie jest wymagane natychmiastowe działanie klienta.** Dla istniejących klientów warstwa dynamiczna, która była używana 1 lutego 2020 r., określa nową domyślną warstwę. W wyniku tej zmiany nie ma wpływu na ceny ani wydajność. W przyszłości klienci usług Azure AD DS będą musieli ocenić wymagania dotyczące wydajności, ponieważ zmieniają się ich rozmiar katalogu i charakterystyka obciążenia. Przełączanie między warstwami usług będzie nadal operacją bez przestojów i nie będziemy już automatycznie przenosić klientów do nowych warstw na podstawie wzrostu ich katalogu. Ponadto nie będzie podwyżek cen, a nowe ceny będą zgodne z naszym obecnym modelem rozliczeń. Aby uzyskać więcej informacji, zobacz [dokumentację jednostek SKU usługi Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) i stronę [cennika usług domenowych usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — luty 2020 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich
 
W lutym 2020 r. dodaliśmy do galerii aplikacji 31 nowych aplikacji z obsługą federacji: 

[Platforma patentowa IamIP](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [NS1 SSO For Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), In Case of Crisis - Portal [online](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [BIC Cloud Design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [Beekeeper Azure AD Data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [Korn Ferry Assessments](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada Command](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB Navigate](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), New [Relic (Limited Release)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium](https://admin.thulium.com/login/instance), [Ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), Szablon [Chooser dla zespołów](https://links.officeatwork.com/templatechooser-download-teams), [Beesy](https://www.beesy.me/index.php/site/login), [Health Support System](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [MURAL](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink dla nauczycieli i szkół](https://www.thinglink.com/), [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [Przedruki Biurko - Artykuł Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nowe łączniki inicjowania obsługi administracyjnej w galerii aplikacji usługi Azure AD — luty 2020 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich
 
Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Aby uzyskać więcej informacji na temat lepszego zabezpieczania organizacji przy użyciu automatycznego inicjowania obsługi administracyjnej konta użytkownika, zobacz [Automatyzacja inicjowania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Obsługa usługi Azure AD dla kluczy zabezpieczeń FIDO2 w środowiskach hybrydowych

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Ogłaszamy publiczną wersję zapoznawczą obsługi usługi Azure AD dla kluczy zabezpieczeń FIDO2 w środowiskach hybrydowych. Użytkownicy mogą teraz używać kluczy zabezpieczeń FIDO2, aby zalogować się do swoich hybrydowych urządzeń z systemem Windows 10 połączonych z usługą Azure i uzyskać bezproblemowe logowanie do swoich zasobów lokalnych i chmurowych. Obsługa środowisk hybrydowych była najczęściej żądaną funkcją od naszych klientów bez hasła, ponieważ początkowo uruchomiliśmy publiczną wersję zapoznawczą dla obsługi FIDO2 na urządzeniach przyłączonych do usługi Azure AD. Uwierzytelnianie bez hasła przy użyciu zaawansowanych technologii, takich jak biometria i kryptografia klucza publicznego/prywatnego, zapewnia wygodę i łatwość użycia przy jednoczesnym zabezpieczeniu. Dzięki tej publicznej wersji zapoznawczej można teraz używać nowoczesnego uwierzytelniania, takiego jak klucze zabezpieczeń FIDO2, aby uzyskać dostęp do tradycyjnych zasobów usługi Active Directory. Aby uzyskać więcej informacji, przejdź do [logowaniasz/r do zasobów lokalnych](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises). 

Aby rozpocząć, odwiedź [włącz klucze zabezpieczeń FIDO2 dla dzierżawy,](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) aby uzyskać instrukcje krok po kroku. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Nowe środowisko moje konto jest teraz ogólnie dostępne

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Mój profil/konto  
**Możliwości produktu:** Środowisko użytkownika końcowego
 
Moje konto, one stop shop dla wszystkich potrzeb zarządzania kontem użytkownika końcowego, jest teraz ogólnie dostępne! Użytkownicy końcowi mogą uzyskać dostęp do tej nowej witryny za pośrednictwem adresu URL lub w nagłówku nowego środowiska Moje aplikacje. Dowiedz się więcej o wszystkich możliwościach samoobsługi, które oferuje nowe środowisko, w [przeglądzie portalu moje konto.](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Aktualizacja adresu URL witryny mojej witryny do myaccount.microsoft.com

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Mój profil/konto  
**Możliwości produktu:** Środowisko użytkownika końcowego
 
Nowy interfejs użytkownika końcowego Moje konto będzie `https://myaccount.microsoft.com` aktualizował swój adres URL w ciągu następnego miesiąca. Więcej informacji na temat środowiska i wszystkich funkcji samoobsługi konta, które oferuje użytkownikom końcowym, znajdziesz w [pomocy portalu Moje konto.](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)

---
 
## <a name="january-2020"></a>Styczeń 2020 r.
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Nowy portal Moje aplikacje jest teraz ogólnie dostępny

**Typ:** Plan zmian  
**Kategoria usługi:** Moje aplikacje  
**Możliwości produktu:** Środowisko użytkownika końcowego
 
Uaktualnij swoją organizację do nowego portalu Moje aplikacje, który jest teraz ogólnie dostępny! Więcej informacji o nowym portalu i kolekcjach znajdziesz w witrynie [Tworzenie kolekcji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Zmieniono nazwę obszarów roboczych w usłudze Azure AD na kolekcje

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Moje aplikacje   
**Możliwości produktu:** Środowisko użytkownika końcowego
 
Obszary robocze, filtry administratorzy mogą skonfigurować do organizowania aplikacji użytkowników, będą teraz określane jako kolekcje. Więcej informacji na temat konfigurowania ich można znaleźć w [witrynie Tworzenie kolekcji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Usługa Azure AD B2C Phone rejestracji i logowania przy użyciu zasad niestandardowych (Public Preview)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C - Zarządzanie tożsamością konsumenta  
**Możliwości produktu:** B2B/B2C
 
Dzięki numerowi telefonu zarejestruj się i zaloguj się, deweloperzy i przedsiębiorstwa mogą zezwolić swoim klientom na rejestrację i logowanie się przy użyciu jednorazowego hasła wysłanego na numer telefonu użytkownika za pośrednictwem wiadomości SMS. Ta funkcja umożliwia również klientowi zmianę numeru telefonu w przypadku utraty dostępu do telefonu. Dzięki możliwości niestandardowych zasad, rejestracji telefonu i logowania umożliwia deweloperom i przedsiębiorstwom komunikowanie się z ich marką za pomocą dostosowywania strony. Dowiedz się, jak [skonfigurować rejestrację telefonu i logowanie się za pomocą niestandardowych zasad w usłudze Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nowe łączniki inicjowania obsługi administracyjnej w galerii aplikacji usługi Azure AD — styczeń 2020 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich
 
Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Aby uzyskać więcej informacji na temat lepszego zabezpieczania organizacji przy użyciu automatycznego inicjowania obsługi administracyjnej konta użytkownika, zobacz [Automatyzacja inicjowania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — styczeń 2020 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich
 
W styczniu 2020 r. dodaliśmy do galerii aplikacji te 33 nowe aplikacje z obsługą federacji: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LeaveBot](https://leavebot.io/#home), [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [SmartWork](https://www.intumit.com/english/SmartWork.html), [Dotcom-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN - Azure AD SSO Gateway dla Oracle E-Business Suite - EBS, PeopleSoft, i JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [Hosted MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB dla Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient Conduct Manager Software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), [Łącznik Squelch Cloud Office365](https://laxmi.squelch.io/login), [Uwierzytelnianie PingFlow](https://app-staging.pingview.io/), [PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai](https://app.sandwai.com/), [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Dwa nowe wykrywanie ochrony tożsamości

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości
 
Dodaliśmy dwa nowe typy wykrywania połączone logowania do ochrony tożsamości: reguły manipulowania podejrzaną skrzynką odbiorczą i niemożliwe podróże. Te wykrywania w trybie offline są wykrywane przez microsoft cloud app security (MCAS) i wpływają na ryzyko użytkownika i logowania w ochronie tożsamości. Aby uzyskać więcej informacji na temat tych wykrywania, zobacz nasze [typy ryzyka logowania](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Breaking Change: Fragmenty identyfikatora URI nie będą przenoszone przez przekierowanie logowania

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika
 
Począwszy od 8 lutego 2020 r., gdy żądanie jest wysyłane do login.microsoftonline.com do logowania użytkownika, usługa dołączy pusty fragment do żądania.  Zapobiega to klasy ataków przekierowania, zapewniając, że przeglądarka usuwa istniejący fragment w żądaniu. Żadna aplikacja nie powinna mieć zależności od tego zachowania. Aby uzyskać więcej informacji, zobacz [Łamanie zmian](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) w dokumentacji platformy tożsamości firmy Microsoft.

---

## <a name="december-2019"></a>Grudzień 2019 r.

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integracja inicjowania obsługi administracyjnej SAP SuccessFactors z usługą Azure AD i lokalną usługą AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi administracyjnej aplikacji  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości

Teraz można zintegrować SAP SuccessFactors jako autorytatywne źródło tożsamości w usłudze Azure AD. Ta integracja ułatwia automatyzację cyklu życia tożsamości end-to-end, w tym przy użyciu zdarzeń opartych na zasobach ludzkich, takich jak nowi pracownicy lub zakończenia, do kontrolowania inicjowania obsługi administracyjnej kont usługi Azure AD.

Aby uzyskać więcej informacji na temat konfigurowania inicjowania obsługi administracyjnej sap SuccessFactors do usługi Azure AD, zobacz samouczek [automatycznego inicjowania obsługi administracyjnej sap SuccessFactors.](https://aka.ms/SAPSuccessFactorsInboundTutorial)

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Obsługa niestandardowych wiadomości e-mail w usłudze Azure AD B2C (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C - Zarządzanie tożsamością konsumenta  
**Możliwości produktu:** B2B/B2C

Teraz możesz używać usługi Azure AD B2C do tworzenia niestandardowych wiadomości e-mail, gdy użytkownicy zarejestrują się, aby korzystać z aplikacji. Korzystając z displaycontrols (obecnie w wersji zapoznawczej) i zewnętrznego dostawcy poczty e-mail (takiego jak [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/)lub niestandardowego interfejsu API REST), można użyć własnego szablonu wiadomości e-mail, **Adresu i** tekstu tematu, a także obsługi lokalizacji i niestandardowych ustawień hasła jednorazowego (OTP).

Aby uzyskać więcej informacji, zobacz [Niestandardowa weryfikacja poczty e-mail w usłudze Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Zastępowanie zasad bazowych domyślnymi ustawieniami zabezpieczeń

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Bezpieczeństwo tożsamości i ochrona tożsamości

W ramach bezpiecznego modelu uwierzytelniania domyślnie usuwamy istniejące zasady ochrony linii bazowej ze wszystkich dzierżawców. Usunięcie to ma zakończyć się pod koniec lutego. Zastąpienie tych zasad ochrony linii bazowej jest domyślne zabezpieczeń. Jeśli używasz zasad ochrony linii bazowej, musisz zaplanować przejście do nowej zasady domyślnych zabezpieczeń lub do dostępu warunkowego. Jeśli nie używasz tych zasad, nie ma żadnych działań do podjęcia.

Aby uzyskać więcej informacji na temat nowych ustawień domyślnych zabezpieczeń, zobacz [Co to są domyślne zabezpieczenia?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Aby uzyskać więcej informacji na temat zasad dostępu warunkowego, zobacz [Wspólne zasady dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## <a name="november-2019"></a>Listopad 2019 r.

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Obsługa atrybutu SameSite i Chrome 80

**Typ:** Plan zmian  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Uwierzytelnianie użytkownika

W ramach bezpiecznego domyślnego modelu plików cookie przeglądarka Chrome 80 zmienia sposób, `SameSite` w jaki traktuje pliki cookie bez atrybutu. Każdy plik cookie, który `SameSite` nie określi atrybutu, będzie traktowany tak, jakby był ustawiony na `SameSite=Lax`, co spowoduje zablokowanie przez Chrome niektórych scenariuszy udostępniania plików cookie między domenami, od których aplikacja może zależeć. Aby zachować starsze zachowanie Chrome, `SameSite=None` możesz użyć tego `Secure` atrybutu i dodać dodatkowy atrybut, aby można było uzyskać dostęp do plików cookie w różnych witrynach tylko za pośrednictwem połączeń HTTPS. Chrome ma zakończyć tę zmianę do 4 lutego 2020 roku.

Zalecamy wszystkim naszym programistom przetestowanie swoich aplikacji, korzystając z tych wskazówek:

- Ustaw domyślną wartość ustawienia **Użyj bezpiecznego pliku cookie** na **Tak**.

- Ustaw wartość domyślną dla atrybutu **SameSite** na **Brak**.

- Dodaj dodatkowy `SameSite` atrybut **Secure**.

Aby uzyskać więcej informacji, zobacz [Nadchodzące zmiany w plikach cookie sameSite w ASP.NET i ASP.NET podstawowe](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) i potencjalne zakłócenia w [witrynach internetowych klientów oraz produktach i usługach firmy Microsoft w wersji Chrome 79 lub nowszej](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Nowa poprawka dla programu Microsoft Identity Manager (MIM) 2016 z dodatkiem Service Pack 2 (SP2)

**Typ:** Stałe  
**Kategoria usługi:** Menedżer tożsamości firmy Microsoft  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości

Pakiet zestawienia poprawek (kompilacja 4.6.34.0) jest dostępny dla programu Microsoft Identity Manager (MIM) 2016 z dodatkiem Service Pack 2 (SP2). Ten pakiet zestawienia rozwiązuje problemy i dodaje ulepszenia, które są opisane w sekcji "Problemy naprawione i ulepszenia dodane w tej aktualizacji".

Aby uzyskać więcej informacji i pobrać pakiet poprawek, zobacz [Microsoft Identity Manager 2016 z dodatkiem Service Pack 2 (kompilacja 4.6.34.0) Dostępny jest pakiet zbiorczy aktualizacji](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Nowy raport aktywności aplikacji usług AD FS ułatwiające migrację aplikacji do usługi Azure AD (public preview)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

Użyj nowego raportu aktywności aplikacji usług federacyjnych Active Directory (AD FS) w witrynie Azure portal, aby określić, które aplikacje mogą być migrowane do usługi Azure AD. Raport ocenia wszystkie aplikacje usług AD FS pod kątem zgodności z usługą Azure AD, sprawdza wszelkie problemy i zawiera wskazówki dotyczące przygotowywania poszczególnych aplikacji do migracji.

Aby uzyskać więcej informacji, zobacz [Raport aktywności aplikacji usług AD FS do migracji aplikacji do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nowy przepływ pracy dla użytkowników, aby zażądać zgody administratora (Public Preview)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Kontrola dostępu

Nowy przepływ pracy zgody administratora umożliwia administratorom udzielenie dostępu do aplikacji wymagających zatwierdzenia przez administratora. Jeśli użytkownik próbuje uzyskać dostęp do aplikacji, ale nie może udzielić zgody, może teraz wysłać żądanie zatwierdzenia przez administratora. Żądanie jest wysyłane pocztą e-mail i umieszczane w kolejce, która jest dostępna z witryny Azure portal, do wszystkich administratorów, którzy zostali wyznaczeni jako recenzenci. Po tym, jak recenzent podejmie działania w sprawie oczekującego żądania, użytkownicy żądający są powiadamiani o akcji.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie przepływu pracy zgody administratora (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nowe środowisko konfiguracji tokenu rejestracji aplikacji usługi Azure AD do zarządzania roszczeniami opcjonalnymi (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Środowisko deweloperskie

Nowy blok **konfiguracji tokenu rejestracji aplikacji usługi Azure AD** w portalu Azure zawiera teraz dynamiczną listę opcjonalnych oświadczeń dotyczących ich aplikacji. To nowe środowisko pomaga usprawnić migracje aplikacji usługi Azure AD i zminimalizować błędy konfiguracji oświadczeń opcjonalnych.

Aby uzyskać więcej informacji, zobacz [Podaj opcjonalne oświadczenia do aplikacji usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nowy dwuetapowy przepływ pracy zatwierdzania w zarządzania uprawnieniami usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Zarządzanie uprawnieniami

Wprowadziliśmy nowy dwuetapowy przepływ pracy zatwierdzania, który pozwala wymagać od dwóch osób zatwierdzających zatwierdzenia żądania użytkownika do pakietu dostępu. Na przykład można ustawić go tak, aby menedżer użytkownika żądającego musi najpierw zatwierdzić, a następnie można również wymagać właściciela zasobu do zatwierdzenia. Jeśli jedna z osób zatwierdzających nie zatwierdzi, dostęp nie zostanie przyznany.

Aby uzyskać więcej informacji, zobacz [Zmienianie ustawień żądania i zatwierdzania pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aktualizacje strony Moje aplikacje wraz z nowymi obszarami roboczymi (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwości produktu:** Integracja stron trzecich

Teraz możesz dostosować sposób wyświetlania i uzyskiwania dostępu do odświeżonych aplikacji Moje aplikacje i uzyskiwania do nich dostępu. To nowe środowisko obejmuje również nową funkcję obszarów roboczych, która ułatwia użytkownikom znajdowanie i organizowanie aplikacji.

Aby uzyskać więcej informacji na temat nowego środowiska Moje aplikacje i tworzenia obszarów roboczych, zobacz [Tworzenie obszarów roboczych w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Obsługa identyfikatorów społecznościowych Google dla współpracy usługi Azure AD B2B (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2b  
**Możliwości produktu:** Uwierzytelnianie użytkownika

Nowa obsługa korzystania z identyfikatorów społecznościowych Google (kont Gmail) w usłudze Azure AD ułatwia użytkownikom i partnerom współpracę. Partnerzy nie muszą już tworzyć nowego konta firmy Microsoft i zarządzać nim. Usługa Microsoft Teams w pełni obsługuje teraz użytkowników Google na wszystkich klientach oraz w punktach końcowych uwierzytelniania związanego ze wspólnymi i związanymi z dzierżawą.

Aby uzyskać więcej informacji, zobacz [Dodawanie Google jako dostawcy tożsamości dla użytkowników-gości B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Pomoc techniczna dla urządzeń przenośnych Microsoft Edge dla dostępu warunkowego i logowania jednokrotnego (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Usługa Azure AD dla przeglądarki Microsoft Edge w systemach iOS i Android obsługuje teraz usługę Azure AD i dostęp warunkowy:

- **Logowanie jednokrotne (Logowanie jednokrotne) w programie Microsoft Edge:** Logowanie jednokrotne jest teraz dostępne dla klientów natywnych (takich jak Microsoft Outlook i Microsoft Edge) dla wszystkich aplikacji połączonych z usługą Azure AD.

- **Dostęp warunkowy w programie Microsoft Edge:** Za pomocą zasad dostępu warunkowego opartego na aplikacjach użytkownicy muszą korzystać z przeglądarek chronionych przez usługę Microsoft Intune, takich jak Microsoft Edge.

Aby uzyskać więcej informacji na temat dostępu warunkowego i logowania jednokrotnego w programie Microsoft Edge, zobacz [Microsoft Edge Mobile Support for Conditional Access i Single Sign-on Now Generally Available](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) w blogu. Aby uzyskać więcej informacji na temat konfigurowania aplikacji klienckich przy użyciu [dostępu warunkowego opartego na aplikacji](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) lub dostępu [warunkowego opartego na urządzeniu,](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)zobacz [Zarządzanie dostępem do sieci Web przy użyciu przeglądarki chronionej zasadami usługi Microsoft Intune](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Zarządzanie uprawnieniami usługi Azure AD (dostępność ogólna)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Innych  
**Możliwości produktu:** Zarządzanie uprawnieniami

Zarządzanie uprawnieniami usługi Azure AD to nowa funkcja nadzoru tożsamości, która pomaga organizacjom zarządzać tożsamością i cyklem życia dostępu na dużą skalę. Ta nowa funkcja pomaga automatyzując przepływy pracy żądania dostępu, przydziały dostępu, recenzje i wygasanie między grupami, aplikacjami i witrynami usługi SharePoint Online.

Dzięki zarządzaniu uprawnieniami usługi Azure AD można wydajniej zarządzać dostępem zarówno dla pracowników, jak i dla użytkowników spoza organizacji, którzy potrzebują dostępu do tych zasobów.

Aby uzyskać więcej informacji, zobacz [Co to jest zarządzanie uprawnieniami usługi Azure AD?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzacja inicjowania obsługi administracyjnej konta użytkownika dla tych nowo obsługiwanych aplikacji SaaS

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich  

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla tych nowo zintegrowanych aplikacji:

[Usługa uwierzytelniania tożsamości platformy SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [Oprogramowanie OfficeSpace](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [Macierz priorytetowa](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Aby uzyskać więcej informacji na temat lepszego zabezpieczania organizacji przy użyciu automatycznego inicjowania obsługi administracyjnej konta użytkownika, zobacz [Automatyzacja inicjowania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — listopad 2019 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich

W listopadzie 2019 r. dodaliśmy do galerii aplikacji 21 nowych aplikacji z obsługą federacji:

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access dla członków (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Single Sign On (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [NiebieskiMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [Foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets Idea & Innovation Management](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope User Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW Online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial), [Jisc Student Rejestracja wyborców](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nowa i ulepszona galeria aplikacji usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

Zaktualizowaliśmy galerię aplikacji usługi Azure AD, aby ułatwić znajdowanie wstępnie zintegrowanych aplikacji obsługujących aprowizacji, OpenID Connect i SAML w dzierżawie usługi Azure Active Directory.

Aby uzyskać więcej informacji, zobacz [Dodawanie aplikacji do dzierżawy usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Zwiększono limit długości definicji roli aplikacji ze 120 do 240 znaków

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

Od klientów słyszeliśmy, że limit długości wartości definicji roli aplikacji w niektórych aplikacjach i usługach jest zbyt krótki i wynosi 120 znaków. W odpowiedzi zwiększyliśmy maksymalną długość definicji wartości roli do 240 znaków.

Aby uzyskać więcej informacji na temat korzystania z definicji ról specyficznych dla aplikacji, zobacz [Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie.](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)

---

## <a name="october-2019"></a>Październik 2019 r.

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Wycofanie tożsamościRiskEvent API dla wykrywania ryzyka usługi Azure AD Identity Protection  

**Typ:** Plan zmian  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

W odpowiedzi na opinie deweloperów subskrybenci usługi Azure AD Premium P2 mogą teraz wykonywać złożone zapytania dotyczące danych wykrywania ryzyka usługi Azure AD Identity Protection przy użyciu nowego interfejsu API riskDetection dla programu Microsoft Graph. Istniejąca [wersja beta apiriskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) przestanie zwracać dane około **10 stycznia 2020.** Jeśli organizacja używa interfejsu API identityRiskEvent, należy przejść do nowego interfejsu API riskDetection.

Aby uzyskać więcej informacji na temat nowego interfejsu API wykrywania ryzyka, zobacz [dokumentację referencyjną interfejsu API wykrywania ryzyka](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Obsługa serwera proxy aplikacji dla atrybutu SameSite i Chrome 80

**Typ:** Plan zmian  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwości produktu:** Kontrola dostępu

Na kilka tygodni przed wydaniem przeglądarki Chrome 80 planujemy zaktualizować sposób, w jaki pliki cookie serwera proxy aplikacji traktują atrybut **SameSite.** Wraz z wydaniem Chrome 80 każdy plik cookie, który nie określa atrybutu **SameSite,** będzie traktowany tak, jakby był ustawiony na `SameSite=Lax`.

Aby uniknąć potencjalnie negatywnych skutków wynikających z tej zmiany, aktualizujemy pliki cookie dostępu serwera proxy aplikacji i sesyjne, aby:

- Ustawienie domyślnej wartości ustawienia **Użyj bezpiecznego pliku cookie** na **Tak**.

- Ustawianie wartości domyślnej dla atrybutu **SameSite** na **Brak**.

    >[!NOTE]
    > Pliki cookie dostępu proxy aplikacji zawsze były przesyłane wyłącznie za pośrednictwem bezpiecznych kanałów. Zmiany te dotyczą tylko sesyjnych plików cookie.

Aby uzyskać więcej informacji na temat ustawień plików cookie serwera proxy aplikacji, zobacz [Ustawienia plików cookie dotyczące uzyskiwania dostępu do aplikacji lokalnych w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Rejestracje aplikacji (starsze) i konwergentne zarządzanie aplikacjami z portalu rejestracji aplikacji (apps.dev.microsoft.com) nie będą już dostępne

**Typ:** Plan zmian  
**Kategoria usługi:** N/a  
**Możliwości produktu:** Środowisko deweloperskie

W najbliższej przyszłości użytkownicy korzystający z kont usługi Azure AD nie będą już mogli rejestrować i zarządzać aplikacjami konwergentnymi za pomocą portalu rejestracji aplikacji (apps.dev.microsoft.com) ani rejestrować aplikacji i zarządzać nimi w obszarze rejestracji aplikacji (starszych) w witrynie Azure portal.

Aby dowiedzieć się więcej o nowych środowiskach rejestracji aplikacji, zobacz [rejestracje aplikacji w przewodniku szkoleniowym portalu Azure.](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md)

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Użytkownicy nie muszą już ponownie rejestrować się podczas migracji z usługi MFA dla użytkowników do usługi MFA opartej na dostępie warunkowym

**Typ:** Stałe  
**Kategoria usługi:** Mfa  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Naprawiliśmy znany problem polegający na tym, że gdy użytkownicy musieli ponownie zarejestrować się, jeśli zostali wyłączeni dla uwierzytelniania wieloskładnikowego dla użytkownika (MFA), a następnie włączono uwierzytelnianie wieloskładnikowe za pośrednictwem zasad dostępu warunkowego.

Aby wymagać od użytkowników ponownej rejestracji, można wybrać opcję **Wymagane ponowne rejestrowanie usługi MFA** z metod uwierzytelniania użytkownika w portalu usługi Azure AD. Aby uzyskać więcej informacji na temat migracji użytkowników z usługi MFA dla użytkowników do usługi MFA opartej na dostępie [warunkowym, zobacz Konwertowanie użytkowników z usługi MFA na użytkownika na uwierzytelnianie wieloskładnikowe oparte na dostępie warunkowym](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nowe możliwości przekształcania i wysyłania oświadczeń w tokenie SAML

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Usługi rejestracji jednokrotnej

Dodaliśmy dodatkowe funkcje, które pomogą Ci dostosować i wysłać oświadczenia w tokenie SAML. Te nowe możliwości obejmują:

- Dodatkowe funkcje przekształcania oświadczeń, co pomaga zmodyfikować wartość wysłaną w oświadczeń.

- Możliwość zastosowania wielu przekształceń do jednego oświadczenia.

- Możliwość określenia źródła oświadczeń na podstawie typu użytkownika i grupy, do której należy użytkownik.

Aby uzyskać szczegółowe informacje na temat tych nowych funkcji, w tym sposobu ich używania, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji korporacyjnych.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nowa strona Moje logowania dla użytkowników końcowych w usłudze Azure AD

**Typ:** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnianie (loginy)  
**Możliwości produktu:** Monitorowanie & raportowania

Dodaliśmy nową stronę **Moje logowania** (https://mysignins.microsoft.com) aby umożliwić użytkownikom organizacji wyświetlanie ich najnowszej historii logowania w celu sprawdzenia, czy nie ma nietypowej aktywności. Ta nowa strona umożliwia użytkownikom wyświetlanie:

- Jeśli ktoś próbuje odgadnąć swoje hasło.

- Jeśli osoba atakująca pomyślnie zalogowała się na swoje konto i z jakiej lokalizacji.

- Do jakich aplikacji osoba atakująca próbowała uzyskać dostęp.

Aby uzyskać więcej informacji, zobacz [Użytkownicy mogą teraz sprawdzić ich historii logowania dla bloga o nietypowej aktywności.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066)

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migracja usług domenowych usługi Azure AD (Usługi Azure AD DS) z sieci wirtualnych usługi Azure Resource Manager z sieci wirtualnych usługi Azure Resource Manager

**Typ:** Nowa funkcja  
**Kategoria usługi:** Usługi domenowe usługi Azure AD  
**Możliwości produktu:** Usługi domenowe usługi Azure AD

Dla naszych klientów, którzy utknęli w klasycznych sieciach wirtualnych - mamy dla Ciebie świetną wiadomość! Teraz można przeprowadzić jednorazową migrację z klasycznej sieci wirtualnej do istniejącej sieci wirtualnej Menedżera zasobów. Po przejściu do sieci wirtualnej Menedżera zasobów będziesz mógł korzystać z dodatkowych i uaktualnionych funkcji, takich jak szczegółowe zasady haseł, powiadomienia e-mail i dzienniki inspekcji.

Aby uzyskać więcej informacji, zobacz [Wersja zapoznawcza — migrowanie usług domenowych usługi Azure AD domain z klasycznego modelu sieci wirtualnej do Menedżera zasobów](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Aktualizacje układu umowy strony usługi Azure AD B2C

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C - Zarządzanie tożsamością konsumenta  
**Możliwości produktu:** B2B/B2C

Wprowadziliśmy kilka nowych zmian w wersji 1.2.0 umowy strony dla usługi Azure AD B2C. W tej zaktualizowanej wersji można teraz kontrolować kolejność ładowania elementów, co może również pomóc zatrzymać migotanie, które ma miejsce po załadowaniu arkusza stylów (CSS).

Aby uzyskać pełną listę zmian wprowadzonych w umowie strony, zobacz [dziennik zmian wersji](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aktualizacja do strony Moje aplikacje wraz z nowymi obszarami roboczymi (podgląd publiczny)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Moje aplikacje  
**Możliwości produktu:** Kontrola dostępu

Teraz możesz dostosować sposób wyświetlania i uzyskiwania dostępu użytkowników do nowej usługi Moje aplikacje i uzyskiwania do nich dostępu, w tym korzystanie z nowej funkcji obszarów roboczych, aby ułatwić im znajdowanie aplikacji. Nowa funkcja obszarów roboczych działa jako filtr dla aplikacji, do których użytkownicy organizacji mają już dostęp.

Aby uzyskać więcej informacji na temat rozwijania nowego środowiska Moje aplikacje i tworzenia obszarów roboczych, zobacz [Tworzenie obszarów roboczych w portalu Moje aplikacje (wersja zapoznawcza).](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Obsługa miesięcznego aktywnego modelu rozliczeń opartego na użytkownikach (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** B2C - Zarządzanie tożsamością konsumenta  
**Możliwości produktu:** B2B/B2C

Usługa Azure AD B2C obsługuje teraz miesięczne płatności aktywnych użytkowników (MAU). Rozliczenia mau są oparte na liczbie unikatowych użytkowników z aktywnością uwierzytelniania w ciągu miesiąca kalendarzowego. Obecni klienci mogą przełączyć się na tę nową metodę rozliczeń w dowolnym momencie.

Od 1 listopada 2019 r. wszyscy nowi klienci będą automatycznie rozliczani przy użyciu tej metody. Ta metoda rozliczeń przynosi korzyści klientom dzięki korzyściom kosztowniczym i możliwości planowania z wyprzedzeniem.

Aby uzyskać więcej informacji, zobacz [Uaktualnianie do modelu rozliczeń aktywnych użytkowników miesięcznie](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — październik 2019 r.

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** Integracja stron trzecich

W październiku 2019 r. dodaliśmy do galerii aplikacji 35 nowych aplikacji z obsługą federacji:

[W przypadku kryzysu - Mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [Tact](https://tact.ai/assistant/), [OpusCapita Cash Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), Content [Bindtuning](https://bindtuning.com/login), [HireVue Coordinate - Europa](https://www.hirevue.com/), [HireVue Współrzędne - USOnly](https://www.hirevue.com/), [HireVue Współrzędne - USA](https://www.hirevue.com/), [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [Mail Luck!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Teamie](https://theteamie.com/), [Velocity for Teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Navigate IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), [Mówiąc e-mail dla intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Mówiąc e-mail dla Office 365 Direct (iPhone / Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome Care Navigation System](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html) [Contentful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [Integracja aplikacji SaaS z usługą Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat wyświetlania aplikacji w galerii aplikacji usługi Azure AD, zobacz [Lista aplikacji w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Element menu Zabezpieczenia skonsolidowane w portalu usługi Azure AD

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwości produktu:** Ochrona & zabezpieczeń tożsamości

Teraz można uzyskać dostęp do wszystkich dostępnych funkcji zabezpieczeń usługi Azure AD z nowego elementu menu **Zabezpieczenia** i z paska **wyszukiwania** w witrynie Azure portal. Ponadto nowa strona docelowa **zabezpieczeń** o nazwie **Security — Wprowadzenie**— zawiera łącza do naszej dokumentacji publicznej, wskazówek dotyczących zabezpieczeń i przewodników dotyczących wdrażania.

Nowe menu **Zabezpieczenia** obejmuje:

- Dostęp warunkowy
- Identity Protection
- Security Center
- Bezpieczny wynik tożsamości
- Metody uwierzytelniania
- Mfa
- Raporty o ryzyku — ryzykowni użytkownicy, ryzykowne logowania, wykrywanie ryzyka
- I więcej...

Aby uzyskać więcej informacji, zobacz [Zabezpieczenia — wprowadzenie](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Rozszerzone zasady wygasania grup usługi Office 365 o funkcje autorenewal

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie grupą  
**Możliwości produktu:** Zarządzanie cyklem życia tożsamości

Zasady wygasania grup usługi Office 365 zostały rozszerzone, aby automatycznie odnawiać grupy, które są aktywnie używane przez jej członków. Grupy będą autoryzowane na podstawie aktywności użytkowników we wszystkich aplikacjach usługi Office 365, w tym w programach Outlook, SharePoint i Teams.

To ulepszenie pomaga zmniejszyć powiadomienia o wygaśnięciu grupy i pomaga upewnić się, że aktywne grupy są nadal dostępne. Jeśli masz już aktywne zasady wygasania dla grup usługi Office 365, nie musisz nic robić, aby włączyć tę nową funkcję.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad wygasania dla grup usługi Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Zaktualizowane środowisko tworzenia usług domenowych usługi Azure AD (Usługi Azure AD DS)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Usługi domenowe usługi Azure AD  
**Możliwości produktu:** Usługi domenowe usługi Azure AD

Zaktualizowaliśmy usługi domenowe usługi Azure AD (Azure AD DS), aby uwzględnić nowe i ulepszone środowisko tworzenia, pomagając utworzyć domenę zarządzaną za pomocą zaledwie trzech kliknięć! Ponadto można teraz przekazać i wdrożyć usługi Azure AD DS z szablonu.

Aby uzyskać więcej informacji, zobacz [Samouczek: Tworzenie i konfigurowanie wystąpienia usług domenowych Usługi domenowe Active Directory platformy Azure](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---
