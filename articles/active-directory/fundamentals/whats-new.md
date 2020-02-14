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
ms.date: 01/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5bf9c5c80593066e31b1ff23b6def844f67e65
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185776"
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

## <a name="august-2019"></a>Sierpień 2019 r.

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Ulepszone wyszukiwanie, filtrowanie i sortowanie dla grup jest dostępne w portalu usługi Azure AD (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Z przyjemnością ogłaszamy publiczną wersję zapoznawczą rozszerzonych środowisk związanych z grupami w portalu usługi Azure AD. Te ulepszenia ułatwiają lepsze zarządzanie grupami i listami składowymi, zapewniając:

- Zaawansowane możliwości wyszukiwania, takie jak wyszukiwanie podciągów na listach grup.
- Zaawansowane opcje filtrowania i sortowania na listach elementów członkowskich i właścicieli.
- Nowe możliwości wyszukiwania dla list elementów członkowskich i właścicieli.
- Dokładniejsze liczby grup dla dużych grup.

Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami w Azure Portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Nowe role niestandardowe są dostępne dla zarządzania rejestracją aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** RBAC  
**Możliwość produktu:** Access Control

Role niestandardowe (dostępne w ramach subskrypcji usługi Azure AD P1 lub P2) mogą teraz pomóc zapewnić szczegółowy dostęp, umożliwiając tworzenie definicji ról z określonymi uprawnieniami, a następnie przypisywanie tych ról do określonych zasobów. Obecnie można tworzyć role niestandardowe przy użyciu uprawnień do zarządzania rejestracjami aplikacji, a następnie przypisywania roli do określonej aplikacji. Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [Niestandardowe role administratorów w Azure Active Directory (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Jeśli potrzebujesz dodatkowych uprawnień lub zasobów, które nie są obecnie widoczne, możesz wysłać opinię do naszej [witryny opinii o platformie Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) i dodać Twoje żądanie do naszej mapy aktualizacji.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Nowe dzienniki aprowizacji mogą pomóc w monitorowaniu i rozwiązywaniu problemów z wdrożeniem aprowizacji aplikacji (publiczna wersja zapoznawcza)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Inicjowanie obsługi aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

Nowe dzienniki aprowizacji są dostępne, aby ułatwić monitorowanie i rozwiązywanie problemów z wdrożeniem aprowizacji użytkowników i grup. Te nowe pliki dziennika zawierają informacje o:

- Które grupy zostały pomyślnie utworzone w [usługi ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Jakie role zostały zaimportowane z [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Którzy pracownicy nie zaimportowali z [produktu Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Aby uzyskać więcej informacji, zobacz artykuł [aprowizacji raportów w portalu Azure Active Directory (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nowe raporty zabezpieczeń dla wszystkich administratorów usługi Azure AD (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Ochrona tożsamości  
**Możliwość produktu:** Ochrona tożsamości & zabezpieczenia

Domyślnie wszyscy Administratorzy usługi Azure AD wkrótce będą mogli uzyskiwać dostęp do nowoczesnych raportów zabezpieczeń w ramach usługi Azure AD. Do końca września będzie można użyć transparentu w górnej części nowoczesnych raportów zabezpieczeń, aby powrócić do starych raportów.

Nowoczesne raporty zabezpieczeń zapewniają dodatkowe możliwości ze starszych wersji, w tym:

- Zaawansowane filtrowanie i sortowanie
- Akcje zbiorcze, takie jak odrzucanie ryzyka użytkownika
- Potwierdzenie ochrony jednostek naruszonych lub bezpiecznych
- Stan ryzyka, obejmujący: zagrożone, odrzucone, korygowane i potwierdzone naruszone
- Nowe wykrywania związane z ryzykiem (dostępne dla subskrybentów Azure AD — wersja Premium)

Aby uzyskać więcej informacji, zobacz [ryzykowni użytkownicy](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [ryzykowne logowania](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)i [wykrywanie ryzyka](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Tożsamość zarządzana przypisana przez użytkownika jest dostępna dla Virtual Machines i Virtual Machine Scale Sets (ogólna dostępność)

**Typ:** Nowa funkcja  
**Kategoria usługi:** Zarządzane tożsamości dla zasobów platformy Azure  
**Możliwość produktu:** Środowisko deweloperskie

Tożsamości zarządzane przypisane przez użytkownika są teraz ogólnie dostępne dla Virtual Machines i Virtual Machine Scale Sets. W ramach tego działania platforma Azure może utworzyć tożsamość w dzierżawie usługi Azure AD, która jest zaufana dla używanej subskrypcji, i może być przypisana do co najmniej jednego wystąpienia usługi platformy Azure. Aby uzyskać więcej informacji na temat tożsamości zarządzanych przypisanych przez użytkownika, zobacz [co to jest tożsamość zarządzana dla zasobów platformy Azure?](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Użytkownicy mogą resetować swoje hasła przy użyciu aplikacji mobilnej lub tokenu sprzętowego (ogólna dostępność)

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Samoobsługowe resetowanie hasła  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Użytkownicy, którzy zarejestrowali aplikację mobilną w organizacji, mogą teraz resetować swoje hasła, zatwierdzając powiadomienie w aplikacji Microsoft Authenticator lub wprowadzając kod z aplikacji mobilnej lub tokenu sprzętowego.

Aby uzyskać więcej informacji, zobacz [jak to działa: Samoobsługowe resetowanie hasła w usłudze Azure AD](https://aka.ms/authappsspr). Aby uzyskać więcej informacji na temat środowiska użytkownika, zobacz [Resetowanie własnego hasła służbowego — Omówienie](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignoruje udostępnioną pamięć podręczną MSAL.NET dla scenariuszy w imieniu

**Typ:** FIXED  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Począwszy od biblioteki Azure AD Authentication Library (ADAL.NET) w wersji 5.0.0 — wersja zapoznawcza, deweloperzy aplikacji muszą [serializować jedną pamięć podręczną na konto dla aplikacji sieci Web i interfejsów API sieci Web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). W przeciwnym razie niektóre scenariusze korzystające z [przepływu w imieniu](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)i konkretne przypadki użycia `UserAssertion`mogą spowodować podniesienie uprawnień. Aby uniknąć tej luki w zabezpieczeniach, ADAL.NET teraz ignoruje bibliotekę uwierzytelniania Microsoft dla udostępnionej pamięci podręcznej usługi dotnet (MSAL.NET) dla scenariuszy w imieniu użytkownika.

Aby uzyskać więcej informacji o tym problemie, zobacz temat [Luka w zabezpieczeniach Azure Active Directory podniesienia uprawnień w bibliotece uwierzytelniania](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — sierpień 2019

**Typ:** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwości produktu:** integracja innej firmy

W sierpniu 2019 dodaliśmy te 26 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Projektowi Civic Innovation platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial)Operations Manager, [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport (Europa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), w usłudze [Academy](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [macierze priorytetowe](https://sync.appfluence.com/pmwebng/), [Inativ Cousto](https://cousto.platformers.be/account/login), [MySpace](https://uploadcare.com/accounts/signup/), [Uploadcare Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [Carbonite, CPQSync](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [dostarczanie. Media™ Portal](https://portal.deliver.media), [Chargebee Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [teraźniejszości AD Łączenie](https://www.stashcat.com), [migotanie](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [Watch według kolorów](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [zespół](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB, opieka strategiczna](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Aby uzyskać więcej informacji na temat aplikacji, zobacz [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji na temat tworzenia listy aplikacji w galerii aplikacji usługi Azure AD, zobacz [Wyświetlanie listy aplikacji w galerii aplikacji Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Dostępne są nowe wersje modułów AzureAD PowerShell i AzureADPreview PowerShell

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Różnych  
**Możliwość produktu:** Katalogi

Dostępne są nowe aktualizacje modułów programu PowerShell AzureAD i AzureAD w wersji zapoznawczej:

- Dodano nowy parametr `-Filter` do parametru `Get-AzureADDirectoryRole` w module AzureAD. Ten parametr ułatwia filtrowanie ról w katalogu zwracanych przez polecenie cmdlet.
- Do modułu AzureADPreview dodano nowe polecenia cmdlet, które ułatwiają Definiowanie i przypisywanie ról niestandardowych w usłudze Azure AD, w tym:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Ulepszenia interfejsu użytkownika konstruktora reguł grupy dynamicznej w Azure Portal

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Społeczności

Wprowadziliśmy pewne ulepszenia interfejsu użytkownika dla konstruktora dynamicznej reguły grupy, dostępne w Azure Portal, aby łatwiej skonfigurować nową regułę lub zmienić istniejące reguły. To ulepszenie projektu pozwala tworzyć reguły z maksymalnie pięcioma wyrażeniami zamiast tylko jeden. Zaktualizowaliśmy także listę właściwości urządzenia w celu usunięcia przestarzałych właściwości urządzenia.

Aby uzyskać więcej informacji, zobacz [Zarządzanie dynamicznymi regułami członkostwa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nowe uprawnienie Microsoft Graph aplikacji dostępne do przeglądu dostępu

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Przeglądy dostępu  
**Możliwość produktu:** Zarządzanie tożsamościami

Wprowadziliśmy nowe uprawnienie Microsoft Graph aplikacji `AccessReview.ReadWrite.Membership`, które umożliwia aplikacjom automatyczne tworzenie i pobieranie przeglądów dostępu dla członkostwa w grupach i przypisań aplikacji. To uprawnienie może być używane przez zaplanowane zadania lub jako część automatyzacji, bez konieczności logowania kontekstu użytkownika.

Aby uzyskać więcej informacji, zobacz [przykład jak utworzyć przeglądy dostępu do usługi Azure AD przy użyciu uprawnień aplikacji Microsoft Graph przy użyciu blogu programu PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Dzienniki aktywności usługi Azure AD są teraz dostępne dla wystąpień chmury dla instytucji rządowych w Azure Monitor

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Reporting  
**Możliwość produktu:** Monitorowanie & raportowania

Przyjemnością się ogłaszamy, że dzienniki aktywności usługi Azure AD są teraz dostępne dla wystąpień chmury dla instytucji rządowych w Azure Monitor. Teraz możesz wysyłać dzienniki usługi Azure AD na konto magazynu lub do centrum zdarzeń, aby zintegrować je z narzędziami SIEM, takimi jak [SumoLogic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)i [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Aby uzyskać więcej informacji na temat konfigurowania Azure Monitor, zobacz [dzienniki aktywności usługi Azure AD w Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Aktualizowanie użytkowników do nowego, ulepszonego środowiska informacji o zabezpieczeniach

**Typ:** Zmieniono funkcję  
**Kategoria usługi:**  Uwierzytelnienia (nazwy logowania)   
**Możliwość produktu:** Uwierzytelnianie użytkownika

25 września 2019 zostanie wyłączone stare, nieulepszone środowisko informacyjne zabezpieczeń na potrzeby rejestrowania i zarządzania informacjami o zabezpieczeniach użytkownika i włączania tylko nowej, [ulepszonej wersji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Oznacza to, że użytkownicy nie będą już mogli używać starego środowiska.

Aby uzyskać więcej informacji na temat udoskonalonego środowiska informacji o zabezpieczeniach, zapoznaj się z [dokumentacją administratora](https://aka.ms/securityinfodocs) i [dokumentacją użytkownika](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Aby włączyć to nowe środowisko, musisz:

1. Zaloguj się do Azure Portal jako Administrator globalny lub administrator użytkowników.

2. Przejdź do pozycji **Azure Active Directory > Ustawienia użytkownika > zarządzanie ustawieniami funkcji Podgląd panelu dostępu**.

3. **Użytkownicy mogą korzystać z funkcji w wersji zapoznawczej na potrzeby rejestrowania obszaru informacje zabezpieczające i zarządzania nim** , wybrać opcję **wybrane**, a następnie wybrać grupę użytkowników lub wybrać opcję **wszystkie** , aby włączyć tę funkcję dla wszystkich użytkowników w dzierżawie.

4. W programie * * użytkownicy mogą używać funkcji w wersji zapoznawczej do rejestrowania i zarządzania zabezpieczeniami * * info * * * * — wybierz opcję **Brak**.

5. Zapisz ustawienia.

    Po zapisaniu ustawień nie będziesz mieć już dostępu do starych informacji zabezpieczających.

>[!Important]
>Jeśli te kroki nie zostaną wykonane przed 25 września 2019, dzierżawa Azure Active Directory zostanie automatycznie włączona dla udoskonalonego środowiska. Jeśli masz pytania, skontaktuj się z nami pod adresem registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Żądania uwierzytelniania przy użyciu wpisów logowania będą bardziej ścisłe.

**Typ:** Zmieniono funkcję  
**Kategoria usługi:** Uwierzytelnienia (nazwy logowania)  
**Możliwość produktu:** Norm

Od 2 września 2019 żądanie uwierzytelnienia przy użyciu metody POST będzie bardziej ściśle zweryfikowane względem standardów protokołu HTTP. W przypadku opcji spacje i podwójne cudzysłowy (") nie będą już usuwane z wartości formularza żądania. Te zmiany nie są oczekiwane na podział istniejących klientów i ułatwiają zapewnienie, że żądania wysyłane do usługi Azure AD są niezawodnie obsługiwane za każdym razem.

Aby uzyskać więcej informacji, zobacz [powiadomienia o zmianach w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---
