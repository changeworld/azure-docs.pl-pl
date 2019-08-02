---
title: Co nowego? Informacje o wersji — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Aby dowiedzieć się o nowych za pomocą usługi Azure Active Directory, takie jak najnowszej wersji, znanych problemów i poprawek błędów przestarzałe funkcje i nadchodzących zmian.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d25204e8842eb01ea626966de6c03ae81f41385
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717302"
---
# <a name="whats-new-in-azure-active-directory"></a>What's new in Azure Active Directory?

>Otrzymuj powiadomienia o tym, kiedy należy ponownie odwiedzić Tę stronę pod kątem aktualizacji przez skopiowanie i wklejenie `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` tego adresu ![URL: do czytnika](./media/whats-new/feed-icon-16x16.png) kanału informacyjnego kanału informacyjnego RSS.

Usługa Azure AD odbiera ulepszenia w sposób ciągły. Aby uzyskać najnowsze informacje o najnowszych zmianach, w tym artykule przedstawiono informacje o:

- Zainstalowane najnowsze wersje
- Znane problemy
- Poprawki błędów
- Przestarzałe funkcje
- Plany dotyczące zmian

Ta strona jest aktualizowany co miesiąc, więc ponownie regularnie. Jeśli szukasz elementów, które są starsze niż sześć miesięcy, możesz je znaleźć w archiwum, aby poznać nowości [w Azure Active Directory](whats-new-archive.md).

---

## <a name="july-2019"></a>Lipiec 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Zaplanuj zmianę: Aktualizacja usługi serwera proxy aplikacji do obsługi tylko protokołu TLS 1,2

**Wprowadź** Plan zmiany  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Aby zapewnić sobie Najmocniejsze szyfrowanie, zaczniemy ograniczać dostęp usługi serwera proxy aplikacji tylko do protokołów TLS 1,2. To ograniczenie zostanie początkowo wdrożone dla klientów, którzy już korzystają z protokołów TLS 1,2, więc nie zobaczysz wpływu. Zakończenie korzystania z protokołów TLS 1,0 i TLS 1,1 zostanie zakończone 31 sierpnia 2019. Klienci nadal korzystający z protokołów TLS 1,0 i TLS 1,1 otrzymają zaawansowaną informację na temat przygotowania do tej zmiany.

Aby zachować połączenie z usługą serwera proxy aplikacji w ramach tej zmiany, zalecamy, aby upewnić się, że kombinacje na serwerze klienta i przeglądarki zostały zaktualizowane pod kątem korzystania z protokołu TLS 1,2. Zalecamy również, aby dołączać do aplikacji publikowanych za pomocą usługi serwera proxy aplikacji wszystkie systemy klienckie używane przez pracowników.

Aby uzyskać więcej informacji, zobacz [Dodawanie aplikacji lokalnej dla dostępu zdalnego przy użyciu serwera proxy aplikacji w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Zaplanuj zmianę: Aktualizacje projektu są dostępne dla galerii aplikacji

**Wprowadź** Plan zmiany  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** SSO

Nowe zmiany w interfejsie użytkownika są nachodzące do projektu **dodawania z obszaru galerii** w bloku **Dodawanie aplikacji** . Te zmiany ułatwią łatwiejsze znajdowanie aplikacji obsługujących automatyczne Inicjowanie obsługi, OpenID Connect Connect, SAML (SAML) i logowanie jednokrotne (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Zaplanuj zmianę: Usuwanie adresu IP serwera usługi MFA z adresu IP pakietu Office 365

**Wprowadź** Plan zmiany  
**Kategoria usługi:** Uwierzytelnianie MFA  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości

Usuwamy adres IP serwera usługi MFA z [usługi sieci Web adresu IP pakietu Office 365 i adresu URL](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Jeśli obecnie korzystasz z tych stron do aktualizowania ustawień zapory, upewnij się, że masz również listę adresów IP udokumentowaną w sekcji **wymagania dotyczące zapory serwera usługi Azure** MFA. [ w artykule serwer usługi Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) MFA.

---

### <a name="app-only-tokens-now-require-the-resource-application-web-api-to-exist-in-the-resource-tenant"></a>Tokeny tylko dla aplikacji wymagają teraz, aby aplikacja zasobów (Web API) istniała w dzierżawie zasobów

**Wprowadź** Ustalone  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

26 lipca 2019 zmieniono sposób dostarczania tokenów tylko aplikacji za pomocą uprawnień do przydzielenia [poświadczeń klienta](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Wcześniej aplikacje mogły uzyskać tokeny do wywoływania innych aplikacji, niezależnie od tego, czy aplikacja kliencka była w dzierżawie. Zaktualizowaliśmy to zachowanie, więc zasoby o pojedynczej dzierżawie nazywane czasami interfejsami API sieci Web mogą być wywoływane tylko przez aplikacje klienckie, które znajdują się w dzierżawie zasobów.

Jeśli aplikacja nie znajduje się w dzierżawie zasobów, zostanie wyświetlony komunikat o błędzie z informacją o `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` tym, aby rozwiązać ten problem, musisz utworzyć jednostkę usługi App Service w dzierżawie przy użyciu [punktu końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) lub [programu PowerShell. ](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell)dzięki temu dzierżawca otrzymuje uprawnienia aplikacji do działania w ramach dzierżawy.

Aby uzyskać więcej informacji, zobacz [co nowego w uwierzytelnianiu?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Istniejąca zgoda między klientem i interfejsem API nadal nie jest wymagana. Aplikacje powinny nadal przeprowadzać własne testy autoryzacji.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nowe logowanie za pomocą hasła do usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Klienci usługi Azure AD mogą teraz ustawiać zasady zarządzania kluczami zabezpieczeń FIDO2 dla użytkowników i grup organizacji. Użytkownicy końcowi mogą również rejestrować swoje klucze zabezpieczeń, używając kluczy do logowania się do swoich kont Microsoft w witrynach sieci Web na urządzeniach z obsługą FIDO, a także logowania do urządzeń z systemem Windows 10 przyłączonych do usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Włączanie logowania bezhasła w usłudze Azure AD (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) w celu uzyskania informacji związanych z administratorem i [Konfigurowanie informacji zabezpieczających do korzystania z klucza zabezpieczeń (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) dla informacji związanych z użytkownikiem końcowym.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacja usługi Azure AD — lipiec 2019

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm

W lipcu 2019 dodaliśmy te 18 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [jasnozielony wzorzec omnichannel Center contacting](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [sprytne Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access for Ethidex zgodność z pakietem Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [pogłoskami narosłymi](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [ Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [wzniesienie](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://stage.twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzowanie aprowizacji kont użytkowników dla tych nowo obsługiwanych aplikacji SaaS

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Monitorowanie i raportowanie

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Katalog federacyjny](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzowanie aprowizacji użytkowników do aplikacji SaaS przy użyciu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nowy tag usługi Azure AD Domain Services dla sieciowej grupy zabezpieczeń

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Azure AD Domain Services  
**Możliwość produktu:** Azure AD Domain Services

Jeśli masz już możliwość zarządzania długimi listami adresów IP i zakresów, możesz użyć nowego tagu usługi sieciowej **AzureActiveDirectoryDomainServices** w grupie zabezpieczeń sieci platformy Azure, aby zabezpieczyć ruch przychodzący do Azure AD Domain Services wirtualnej podsieć sieciowa.

Aby uzyskać więcej informacji na temat tego nowego tagu usługi, zobacz [sieciowe grupy zabezpieczeń dla Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/network-considerations#default-network-service-group).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nowe inspekcje zabezpieczeń dla Azure AD Domain Services (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Azure AD Domain Services  
**Możliwość produktu:** Azure AD Domain Services

Z przyjemnością ogłaszamy wydanie inspekcji zabezpieczeń usługi Azure AD Domain Service do publicznej wersji zapoznawczej. Inspekcja zabezpieczeń ułatwia zapewnienie krytycznego wglądu w usługi uwierzytelniania przez przesyłanie strumieniowe zdarzeń inspekcji zabezpieczeń do zasobów kierowanych, takich jak Azure Storage, obszary robocze platformy Azure Log Analytics i centrum zdarzeń Azure, przy użyciu usługi Azure AD Domain Service Portal.

Aby uzyskać więcej informacji, zobacz [Włączanie inspekcji zabezpieczeń dla Azure AD Domain Services (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nowe metody uwierzytelniania użycie & Insights (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Samodzielne resetowanie hasła  
**Możliwość produktu:** Monitorowanie i raportowanie

Nowe metody uwierzytelniania użycie & raporty usługi Insights mogą pomóc zrozumieć, jak funkcje takie jak uwierzytelnianie wieloskładnikowe i Samoobsługowe resetowanie haseł są rejestrowane i używane w organizacji, w tym liczbę zarejestrowanych Użytkownicy każdej funkcji, jak często funkcja samoobsługowego resetowania hasła jest używana do resetowania haseł i metody resetowania.

Aby uzyskać więcej informacji, zobacz [metody uwierzytelniania użycie & Insights (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nowe raporty zabezpieczeń są dostępne dla wszystkich administratorów usługi Azure AD (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Identity Protection  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości

Wszyscy Administratorzy usługi Azure AD mogą teraz wybrać baner u góry istniejących raportów zabezpieczeń, na przykład raport dotyczący **użytkowników oflagowanych w związku z ryzykiem** , aby zacząć korzystać z nowego środowiska zabezpieczeń, jak pokazano w raportach **ryzykownych użytkowników** i **ryzykowne logowania** . Wraz z upływem czasu wszystkie raporty zabezpieczeń będą przenoszone ze starszych wersji do nowych wersji, a nowe raporty udostępniają następujące dodatkowe możliwości:

- Zaawansowane filtrowanie i sortowanie

- Akcje zbiorcze, takie jak odrzucanie ryzyka użytkownika

- Potwierdzenie ochrony jednostek naruszonych lub bezpiecznych

- Stan ryzyka, obejmujący: Na ryzyko odrzucone, korygowane i potwierdzone naruszone

Aby uzyskać więcej informacji, zobacz Raport [dotyczący ryzykownych użytkowników](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-users-report) i [raport dotyczący ryzykownych](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-sign-ins-report)logowań.

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nowe inspekcje zabezpieczeń dla Azure AD Domain Services (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Azure AD Domain Services  
**Możliwość produktu:** Azure AD Domain Services

Z przyjemnością ogłaszamy wydanie inspekcji zabezpieczeń usługi Azure AD Domain Service do publicznej wersji zapoznawczej. Inspekcja zabezpieczeń ułatwia zapewnienie krytycznego wglądu w usługi uwierzytelniania przez przesyłanie strumieniowe zdarzeń inspekcji zabezpieczeń do zasobów kierowanych, takich jak Azure Storage, obszary robocze platformy Azure Log Analytics i centrum zdarzeń Azure, przy użyciu usługi Azure AD Domain Service Portal.

Aby uzyskać więcej informacji, zobacz [Włączanie inspekcji zabezpieczeń dla Azure AD Domain Services (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nowa Federacja usługi B2B Direct z użyciem protokołu SAML/WS-karmionego (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** B2B  
**Możliwość produktu:** B2B/B2C

Federacja bezpośrednia pomaga uprościć pracę z partnerami, których rozwiązanie do zarządzania tożsamościami zarządzanymi przez dział IT nie jest usługą Azure AD, pracując z systemami tożsamości, które obsługują standardy SAML lub WS-workd. Po skonfigurowaniu bezpośredniej relacji Federacji z partnerem każdy nowy użytkownik-Gość, który Cię zaprosił z tej domeny, może współpracować z użytkownikiem przy użyciu istniejącego konta organizacji, dzięki czemu środowisko użytkownika dla Gości jest bardziej bezproblemowe.

Aby uzyskać więcej informacji, zobacz [bezpośrednia Federacja z AD FSami i dostawcami innych firm dla użytkowników-Gości (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzowanie aprowizacji kont użytkowników dla tych nowo obsługiwanych aplikacji SaaS

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Monitorowanie i raportowanie

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Katalog federacyjny](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzacja aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nowe sprawdzenie dla zduplikowanych nazw grup w portalu usługi Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Współpraca

Teraz po utworzeniu lub zaktualizowaniu nazwy grupy w portalu usługi Azure AD sprawdzimy, czy duplikat istniejącej nazwy grupy jest duplikowany w zasobie. Jeśli okaże się, że nazwa jest już używana przez inną grupę, zostanie wyświetlony monit o zmianę nazwy.

Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami w portalu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Usługa Azure AD obsługuje teraz statyczne parametry zapytań w identyfikatorach URI odpowiedzi (redirect)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Aplikacje usługi Azure AD mogą teraz rejestrować i używać identyfikatorów URI odpowiedzi (redirect) przy użyciu statycznych parametrów zapytania ( `https://contoso.com/oauth2?idp=microsoft`na przykład) dla żądań OAuth 2,0. Statyczny parametr zapytania jest uzależniony od ciągu dla identyfikatorów URI odpowiedzi, podobnie jak każda inna część identyfikatora URI odpowiedzi. Jeśli nie ma żadnego zarejestrowanego ciągu zgodnego z zdekodowanym adresem URL, żądanie zostanie odrzucone. Jeśli zostanie znaleziony identyfikator URI odpowiedzi, cały ciąg jest używany do przekierowania użytkownika, łącznie z parametrem zapytania statycznego.

Dynamiczne identyfikatory URI odpowiedzi są nadal zabronione, ponieważ stanowią zagrożenie bezpieczeństwa i nie mogą być używane do przechowywania informacji o stanie w ramach żądania uwierzytelnienia. W tym celu należy użyć `state` parametru.

Obecnie ekrany rejestracji aplikacji Azure Portal nadal blokują parametry zapytania. Można jednak ręcznie edytować manifest aplikacji, aby dodawać i testować parametry zapytania w aplikacji. Aby uzyskać więcej informacji, zobacz [co nowego w uwierzytelnianiu?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Dzienniki aktywności (interfejsy API MS Graph) dla usługi Azure AD są teraz dostępne za pomocą poleceń cmdlet programu PowerShell

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Monitorowanie i raportowanie

Przyjemnością ogłaszamy, że dzienniki aktywności usługi Azure AD (raporty inspekcji i logowania) są teraz dostępne za pomocą modułu Azure AD PowerShell. Wcześniej można było tworzyć własne skrypty za pomocą punktów końcowych MS interfejs API programu Graph i teraz rozszerzamy tę możliwość na polecenia cmdlet programu PowerShell.

Aby uzyskać więcej informacji na temat sposobu korzystania z tych poleceń cmdlet, zobacz [polecenia cmdlet programu PowerShell usługi Azure AD na potrzeby raportowania](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Zaktualizowano kontrolki filtru dla dzienników inspekcji i logowania w usłudze Azure AD

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Monitorowanie i raportowanie

Zaktualizowaliśmy raporty inspekcji i logowania, dzięki czemu można teraz zastosować różne filtry bez konieczności dodawania ich jako kolumn na ekranach raportów. Ponadto możesz wybrać liczbę filtrów, które mają być wyświetlane na ekranie. Wszystkie te aktualizacje współpracują ze sobą, aby ułatwić ich odczytywanie i zwiększanie zakresu do Twoich potrzeb.

Aby uzyskać więcej informacji o tych aktualizacjach, zobacz [filtrowanie dzienników inspekcji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) i [filtrowanie działań związanych z logowaniem](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Czerwiec 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nowy interfejs API riskDetections dla Microsoft Graph (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Identity Protection  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości

Z przyjemnością ogłaszamy nowy interfejs API riskDetections dla Microsoft Graph jest teraz dostępny w publicznej wersji zapoznawczej. Ten nowy interfejs API umożliwia wyświetlenie listy wykrytych użytkowników związanych z ochroną tożsamości w organizacji oraz ich wykrycia. Możesz również użyć tego interfejsu API, aby efektywniej wykonywać zapytania dotyczące wykrywania ryzyka, w tym szczegółowe informacje o typie wykrywania, stanie, poziomie i innych.

Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą interfejsu API wykrywania zagrożeń](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — czerwiec 2019

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm

W czerwcu 2019 dodaliśmy te 22 nowe aplikacje z obsługą Federacji do galerii aplikacji:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Klient sieci VPN platformy Azure](https://portal.azure.com/), [wydatki](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [pomocnik pomocnika](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz w biurze](https://me.secure.mercedes-benz.com/), [skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk Authentication SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager for Oracle Handel detaliczny, program Oracle Access Manager dla Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatyzowanie aprowizacji kont użytkowników dla tych nowo obsługiwanych aplikacji SaaS

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Monitorowanie i raportowanie

Teraz można zautomatyzować tworzenie, aktualizowanie i usuwanie kont użytkowników dla nowo zintegrowanych aplikacji:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Wysłannika](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przy użyciu funkcji automatycznego inicjowania obsługi kont użytkowników, zobacz [Automatyzowanie aprowizacji użytkowników do aplikacji SaaS przy użyciu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Wyświetlanie postępu w czasie rzeczywistym usługi Azure AD Provisioning

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwość produktu:** Zarządzanie cyklem życia tożsamości

Zaktualizowaliśmy środowisko aprowizacji usługi Azure AD, aby uwzględnić nowy pasek postępu, który pokazuje, jak daleko jesteś w procesie aprowizacji użytkowników. To zaktualizowane środowisko zawiera również informacje o liczbie użytkowników, których zainicjowano w bieżącym cyklu, a także o tym, jak wiele użytkowników zostało zainicjowanych do tej pory.

Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu aprowizacji użytkowników](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Znakowanie firmowe pojawia się teraz na ekranach Wyloguj się i błędy

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Zaktualizowaliśmy usługę Azure AD, dzięki czemu logo firmy pojawia się teraz na ekranach Wyloguj i błędy, a także na stronie logowania. Nie musisz nic robić, aby włączyć tę funkcję, usługa Azure AD po prostu korzysta z zasobów, które zostały już skonfigurowane w obszarze **znakowania firmowego** Azure Portal.

Aby uzyskać więcej informacji na temat konfigurowania znakowania firmowego, zobacz [Dodawanie znakowania do stron Azure Active Directory organizacji](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Serwer uwierzytelniania wieloskładnikowego (MFA) systemu Azure nie jest już dostępny dla nowych wdrożeń

**Wprowadź** Przestarzałe  
**Kategoria usługi:** Uwierzytelnianie MFA  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości

Od 1 lipca 2019 firma Microsoft nie będzie już oferować serwera MFA dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego w swojej organizacji, muszą teraz korzystać z uwierzytelniania wieloskładnikowego opartego na chmurze. Klienci, którzy aktywowali serwer usługi MFA przed 1 lipca, nie zobaczą zmiany. Nadal będzie można pobrać najnowszą wersję, pobrać przyszłe aktualizacje i wygenerować poświadczenia aktywacji.

Aby uzyskać więcej informacji, zobacz [wprowadzenie do serwera usługi Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)MFA. Aby uzyskać więcej informacji na temat uwierzytelniania wieloskładnikowego opartego na chmurze, zobacz [Planowanie wdrożenia usługi Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)MFA opartego na chmurze.

---

## <a name="may-2019"></a>2019 maja

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Zmiana usługi: Przyszła obsługa protokołów TLS 1,2 w usłudze serwera proxy aplikacji

**Wprowadź** Plan zmiany  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Aby pomóc w zapewnieniu najlepszej klasy szyfrowania dla naszych klientów, ograniczamy dostęp do protokołów TLS 1,2 w usłudze serwera proxy aplikacji. Ta zmiana jest stopniowo wprowadzana do klientów, którzy już korzystają z protokołów TLS 1,2, dlatego nie będzie można zobaczyć żadnych zmian.

Wycofanie protokołu TLS 1,0 i TLS 1,1 występuje 31 sierpnia 2019, ale postanowimy dodatkowe zaawansowane powiadomienie, dzięki czemu będziesz mieć czas na przygotowanie się do tej zmiany. Aby przygotować się do tej zmiany, upewnij się, że kombinacja klient-serwer i przeglądarka-serwer, łącznie z klientami używanymi przez użytkowników do uzyskiwania dostępu do aplikacji publikowanych za pomocą serwera proxy aplikacji, zostały zaktualizowane do korzystania z protokołu TLS 1,2 do obsługi połączenia z aplikacją Usługa serwera proxy. Aby uzyskać więcej informacji, zobacz [Dodawanie aplikacji lokalnej dla dostępu zdalnego przy użyciu serwera proxy aplikacji w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Użyj raportu Użycie i szczegółowe informacje, aby wyświetlić dane logowania związane z aplikacją

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Monitorowanie i raportowanie

Możesz teraz użyć raportu Użycie i szczegółowe informacje znajdującego się w obszarze aplikacje dla **przedsiębiorstw** Azure Portal, aby uzyskać widok skoncentrowany na aplikacji, w tym informacje o:

- Najpopularniejsze używane aplikacje dla Twojej organizacji

- Aplikacje z największą liczbą nieudanych logowań

- Najważniejsze błędy logowania dla każdej aplikacji

Aby uzyskać więcej informacji na temat tej funkcji, zobacz [raport dotyczący użycia i usługi Insights w portalu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatyzowanie aprowizacji użytkowników w aplikacjach w chmurze przy użyciu usługi Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Monitorowanie i raportowanie

Postępuj zgodnie z tymi nowymi samouczkami, aby użyć usługi Azure AD Provisioning w celu zautomatyzowania tworzenia, usuwania i aktualizowania kont użytkowników dla następujących aplikacji opartych na chmurze:

- [Współspotykanie](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Możesz również wykonać czynności opisane w samouczku dotyczącym usługi [Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), który zawiera informacje o sposobach aprowizacji obiektów grup.

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przez automatyczne Inicjowanie obsługi kont użytkowników, zobacz [Automatyzowanie aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Ocena bezpiecznego tożsamości jest teraz dostępna w usłudze Azure AD (ogólna dostępność)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** ND  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości

Teraz można monitorować i ulepszać stan zabezpieczeń tożsamości za pomocą funkcji "Funkcja bezpiecznego oceny" w usłudze Azure AD. Funkcja oceny zabezpieczeń tożsamości używa jednego pulpitu nawigacyjnego, aby ułatwić:

- Obiektywnie Zmierz stan zabezpieczeń tożsamości na podstawie wyniku z zakresu od 1 do 223.

- Planowanie ulepszeń w zakresie zabezpieczeń tożsamości

- Zapoznaj się z sukcesem ulepszeń zabezpieczeń

Aby uzyskać więcej informacji na temat funkcji oceny zabezpieczeń tożsamości, zobacz artykuł [co to jest bezpieczna ocena tożsamości w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nowe środowisko Rejestracje aplikacji jest teraz dostępne (ogólna dostępność)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Środowisko deweloperskie

Nowe środowisko [rejestracje aplikacji](https://aka.ms/appregistrations) jest teraz ogólnie dostępne. To nowe środowisko obejmuje wszystkie kluczowe funkcje, które znasz z Azure Portal i portalu rejestracji aplikacji, i ulepszają je w następujący sposób:

- **Lepsza Zarządzanie aplikacjami.** Zamiast oglądać aplikacje w różnych portalach, możesz teraz zobaczyć wszystkie Twoje aplikacje w jednej lokalizacji.

- **Uproszczona rejestracja aplikacji.** W ulepszonym środowisku nawigacyjnym funkcji wyboru uprawnień odnowionych teraz łatwiej jest zarejestrować aplikacje i zarządzać nimi.

- **Bardziej szczegółowe informacje.** Możesz znaleźć więcej szczegółowych informacji o aplikacji, w tym przewodnikach Szybki Start i nie tylko.

Aby uzyskać więcej informacji, zobacz [Microsoft Identity platform](https://docs.microsoft.com/azure/active-directory/develop/) i [środowisko rejestracje aplikacji są teraz ogólnie dostępne!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) ogłoszenie blogu.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nowe funkcje dostępne w interfejsie API użytkowników ryzykownych usługi Identity Protection

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Identity Protection  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości

Z przyjemnością ogłaszamy, że możesz teraz używać interfejsu API ryzykownych użytkowników, aby pobierać historię ryzyka użytkowników, odrzucać ryzykownych użytkowników i potwierdzać bezpieczeństwo użytkowników. Ta zmiana ułatwia efektywniejsze aktualizowanie stanu ryzyka dla użytkowników i zrozumienie ich historii ryzyka.

Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą interfejsu API ryzykownych użytkowników](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — maj 2019

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm

W maju 2019 dodaliśmy następujące 21 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [prawdziwe linki](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Signer](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Display](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [TEMPLAFY](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), resystems, [META4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial) [Quantum Miejsce pracy](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), interfejsy API usługi webMethods, [](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial) [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Udoskonalone środowiska tworzenia i zarządzania grupami w portalu usługi Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Współpraca

Wprowadzono ulepszenia dotyczące środowisk związanych z grupami w portalu usługi Azure AD. Te udoskonalenia umożliwiają administratorom lepsze zarządzanie listami grup, listami członków i udostępnianiem dodatkowych opcji tworzenia.

Ulepszenia obejmują:

- Podstawowe filtrowanie według typu członkostwa i typu grupy.

- Dodanie nowych kolumn, takich jak źródło i adres E-mail.

- Możliwość łatwego usuwania grup, elementów członkowskich i list właścicieli.

- Możliwość wybrania adresu e-mail i dodania właścicieli podczas tworzenia grupy.

Aby uzyskać więcej informacji, zobacz [Tworzenie grupy podstawowej i Dodawanie członków przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurowanie zasad nazewnictwa dla grup Office 365 w portalu usługi Azure AD (ogólna dostępność)

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Współpraca

Administratorzy mogą teraz konfigurować zasady nazewnictwa dla grup pakietu Office 365 przy użyciu portalu usługi Azure AD. Ta zmiana pomaga wymusić spójne konwencje nazewnictwa dla grup pakietu Office 365 utworzonych lub edytowanych przez użytkowników w organizacji.

Zasady nazewnictwa dla grup pakietu Office 365 można skonfigurować na dwa różne sposoby:

- Zdefiniuj prefiksy lub sufiksy, które są automatycznie dodawane do nazwy grupy.

- Przekaż dostosowany zestaw zablokowanych słów dla swojej organizacji, które nie są dozwolone w nazwach grup (na przykład "dyrektor generalny, ListaPłac, HR").

Aby uzyskać więcej informacji, zobacz wymuszanie [zasad nazewnictwa dla grup pakietu Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Punkty końcowe interfejsu API Microsoft Graph są teraz dostępne dla dzienników aktywności usługi Azure AD (ogólna dostępność)

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Monitorowanie i raportowanie

Z przyjemnością ogłaszamy ogólną dostępność Microsoft Graph punktów końcowych interfejsu API dla dzienników aktywności usługi Azure AD. W tej wersji można teraz używać wersji 1,0 zarówno dzienników inspekcji usługi Azure AD, jak i interfejsów API logowania.

Aby uzyskać więcej informacji, zobacz [Omówienie interfejsu API dziennika inspekcji usługi Azure AD](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Administratorzy mogą teraz korzystać z dostępu warunkowego dla połączonego procesu rejestracji (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości  

Administratorzy mogą teraz tworzyć zasady dostępu warunkowego do użycia przez łączną stronę rejestracji. Obejmuje to zastosowanie zasad umożliwiających rejestrację, jeśli:

- Użytkownicy znajdują się w zaufanej sieci.

- Użytkownicy są niskim ryzykiem związanym z logowaniem.

- Użytkownicy znajdują się na zarządzanym urządzeniu.

- Użytkownicy zgadzają się na warunki użytkowania organizacji (warunków użytkowania).

Aby uzyskać więcej informacji na temat dostępu warunkowego i resetowania hasła, można zobaczyć [dostęp warunkowy do wpisu w blogu dotyczącego połączenia MFA i rejestracji resetowania haseł usługi Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Aby uzyskać więcej informacji na temat zasad dostępu warunkowego dla połączonego procesu rejestracji, zobacz [zasady dostępu warunkowego dla połączonej rejestracji](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Aby uzyskać więcej informacji na temat funkcji warunków użytkowania usługi Azure AD, zobacz [Azure Active Directory warunki użytkowania](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Kwiecień 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-in-refreshed-azure-ad-identity-protection"></a>Nowe wykrywanie analizy zagrożeń w usłudze Azure AD jest teraz dostępne w odświeżanej Azure AD Identity Protection

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Usługa Azure AD Identity Protection  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości

Wykrywanie analizy zagrożeń w usłudze Azure AD jest teraz dostępne w odświeżanej Azure AD Identity Protection. Ta nowa funkcja pomaga wskazać aktywność użytkownika nietypową dla określonego użytkownika lub zgodną ze znanymi wzorcami ataków opartymi na wewnętrznej i zewnętrznej analizie zagrożeń firmy Microsoft.

Aby uzyskać więcej informacji na temat odświeżonej wersji Azure AD Identity Protection, zobacz [cztery główne Azure AD Identity Protection ulepszenia znajdują się teraz w publicznym blogu w wersji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) zapoznawczej, a [co to jest Azure Active Directory Identity Protection (odświeżane)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) art. Aby uzyskać więcej informacji o wykrywaniu analizy zagrożeń w usłudze Azure AD, zobacz artykuł dotyczący [zdarzeń dotyczących ryzyka Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Zarządzanie prawami w usłudze Azure AD jest teraz dostępne (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Nadzór nad tożsamościami  
**Możliwość produktu:** Nadzór nad tożsamościami

Zarządzanie prawami usługi Azure AD, teraz w publicznej wersji zapoznawczej, ułatwia klientom delegowanie zarządzania pakietami dostępu, które określają sposób, w jaki pracownicy i partnerzy biznesowi mogą żądać dostępu, którzy muszą zatwierdzić i jak długo mają dostęp. Pakiety dostępu mogą zarządzać członkostwem w grupach usługi Azure AD i Office 365, przypisaniach ról w aplikacjach dla przedsiębiorstw i przypisaniach ról dla witryn programu SharePoint Online. Przeczytaj więcej na temat zarządzania uprawnieniami w temacie [Omówienie zarządzania prawami usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Aby dowiedzieć się więcej o szerokiej części funkcji Azure AD Identity Governance, w tym o Privileged Identity Management, przeglądach dostępu i warunkach użytkowania, zobacz [co to jest Azure AD Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurowanie zasad nazewnictwa dla grup Office 365 w portalu usługi Azure AD (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Współpraca

Administratorzy mogą teraz konfigurować zasady nazewnictwa dla grup pakietu Office 365 przy użyciu portalu usługi Azure AD. Ta zmiana pomaga wymusić spójne konwencje nazewnictwa dla grup pakietu Office 365 utworzonych lub edytowanych przez użytkowników w organizacji.

Zasady nazewnictwa dla grup pakietu Office 365 można skonfigurować na dwa różne sposoby:

- Zdefiniuj prefiksy lub sufiksy, które są automatycznie dodawane do nazwy grupy.

- Przekaż dostosowany zestaw zablokowanych słów dla swojej organizacji, które nie są dozwolone w nazwach grup (na przykład "dyrektor generalny, ListaPłac, HR").

Aby uzyskać więcej informacji, zobacz wymuszanie [zasad nazewnictwa dla grup pakietu Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Dzienniki aktywności usługi Azure AD są teraz dostępne w Azure Monitor (ogólna dostępność)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Monitorowanie i raportowanie

Aby pomóc w rozwiązaniu opinii na temat wizualizacji z dziennikami aktywności usługi Azure AD, wprowadzamy nową funkcję Insights w Log Analytics. Ta funkcja ułatwia uzyskiwanie szczegółowych informacji o zasobach usługi Azure AD przy użyciu interaktywnych szablonów, nazywanych skoroszytami. Te wstępnie skompilowane skoroszyty mogą zawierać szczegóły dotyczące aplikacji lub użytkowników, a także:

- **Logowania.** Zawiera szczegółowe informacje dotyczące aplikacji i użytkowników, w tym lokalizacji logowania, systemu operacyjnego lub klienta przeglądarki oraz wersji, a także liczbę logowań zakończonych powodzeniem lub niepowodzeniem.

- **Starsza wersja uwierzytelniania i dostęp warunkowy.** Zawiera szczegółowe informacje dotyczące aplikacji i użytkowników korzystających ze starszego uwierzytelniania, w tym użycie usługi uwierzytelniania wieloskładnikowego wyzwalane przez zasady dostępu warunkowego, aplikacje korzystające z zasad dostępu warunkowego itd.

- **Analiza niepowodzeń logowania.** Pomaga ustalić, czy występują błędy logowania z powodu akcji użytkownika, problemów z zasadami lub infrastruktury.

- **Raporty niestandardowe.** Możesz tworzyć nowe lub edytować istniejące skoroszyty, aby pomóc w dostosowaniu funkcji wglądu w dane organizacji.

Aby uzyskać więcej informacji, zobacz [jak używać skoroszytów Azure monitor dla Azure Active Directory raportów](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — kwiecień 2019

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm

W kwietniu 2019 dodaliśmy następujące 21 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks logowania](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial)jednokrotnego, [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [SHIBUMI](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), Kanaper [](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), EduBrite systemu [LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect ](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial)Alibabae, [chmura (Logowanie jednokrotne oparte na rolach)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nowa opcja częstotliwości przeglądy dostępu i wybór wielu ról

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Kontrole uprawnień dostępu  
**Możliwość produktu:** Nadzór nad tożsamościami

Nowe aktualizacje w przeglądach dostępu usługi Azure AD umożliwiają:

- Oprócz wcześniej istniejących opcji co tydzień, comiesiąc, co kwartał i co rok należy zmienić częstotliwość kontroli dostępu do półrocza.

- Podczas tworzenia jednego przeglądu dostępu wybierz wiele ról usługi Azure AD i Azure Resource. W tej sytuacji wszystkie role są skonfigurowane z tymi samymi ustawieniami, a wszyscy recenzenci są powiadamiani w tym samym czasie.

Aby uzyskać więcej informacji na temat sposobu tworzenia przeglądu dostępu, zobacz [Tworzenie przeglądu dostępu do grup lub aplikacji w przeglądach dostępu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect przechodzenia do systemu alertów poczty e-mail, co umożliwia wysyłanie nowych informacji o nadawcy wiadomości e-mail dla niektórych klientów

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** AD Sync  
**Możliwość produktu:** Platforma

Azure AD Connect jest w trakcie przechodzenia do naszych systemów alertów e-mail, co może spowodować, że niektórzy klienci są nowym nadawcą poczty e-mail. Aby rozwiązać ten problemy, należy dodać `azure-noreply@microsoft.com` do listy dozwolonych organizacji lub nie będzie można kontynuować otrzymywania ważnych alertów z pakietu Office 365, platformy Azure ani usług synchronizacji.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Zmiany sufiksu UPN są teraz pomyślne między domenami federacyjnymi w Azure AD Connect

**Wprowadź** Ustalone  
**Kategoria usługi:** AD Sync  
**Możliwość produktu:** Platforma

Teraz można pomyślnie zmienić sufiks nazwy UPN użytkownika z jednej domeny federacyjnej na inną domenę federacyjną w Azure AD Connect. Ta poprawka oznacza, że w trakcie cyklu synchronizacji nie ma już komunikatu o błędzie FederatedDomainChangeError lub odebrać wiadomość e-mail z powiadomieniem "nie można zaktualizować tego obiektu w Azure Active Directory, ponieważ atrybut [ FederatedUser. UserPrincipalName] jest nieprawidłowy. Zaktualizuj wartość w lokalnych usługach katalogowych.

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów podczas synchronizacji](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Zwiększone bezpieczeństwo przy użyciu zasad dostępu warunkowego opartego na ochronie aplikacji w usłudze Azure AD (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości

Dostęp warunkowy oparty na ochronie aplikacji jest teraz dostępny przy użyciu zasad **Wymagaj ochrony aplikacji** . Nowe zasady ułatwiają zwiększenie bezpieczeństwa organizacji, pomagając w uniknięciu:

- Użytkownicy uzyskują dostęp do aplikacji bez licencji na Microsoft Intune.

- Użytkownicy nie mogą uzyskać Microsoft Intune zasad ochrony aplikacji.

- Użytkownicy uzyskują dostęp do aplikacji bez skonfigurowanych zasad ochrony aplikacji Microsoft Intune.

Aby uzyskać więcej informacji, zobacz [jak wymagać zasad ochrony aplikacji dla dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nowa obsługa logowania jednokrotnego w usłudze Azure AD i dostępu warunkowego w programie Microsoft Edge (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości

Ulepszono obsługę usługi Azure AD dla przeglądarki Microsoft Edge, w tym zapewnianie nowej obsługi logowania jednokrotnego usługi Azure AD i dostępu warunkowego. Jeśli wcześniej używasz Managed Browser Microsoft Intune, możesz teraz użyć przeglądarki Microsoft Edge.

Aby uzyskać więcej informacji na temat konfigurowania urządzeń i aplikacji oraz zarządzania nimi przy użyciu dostępu warunkowego, zobacz [Wymagaj zarządzanych urządzeń dla dostępu do aplikacji w chmurze z dostępem warunkowym](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) i [Wymagaj zatwierdzonych aplikacji klienckich do uzyskiwania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego ](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Aby uzyskać więcej informacji o sposobach zarządzania dostępem przy użyciu programu Microsoft Edge z zasadami Microsoft Intune, zobacz [Zarządzanie dostępem do Internetu za pomocą przeglądarki Microsoft Intune chronionej przez zasady](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Marzec 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Platforma obsługi tożsamości i obsługa zasad niestandardowych w programie Azure Active Directory B2C jest teraz dostępna (GA)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** B2C — Consumer Identity Management  
**Możliwość produktu:** B2B/B2C

Teraz można tworzyć niestandardowe zasady w Azure AD B2C, w tym następujące zadania, które są obsługiwane na skalę i w ramach umowy SLA platformy Azure:

- Tworzenie i przekazywanie niestandardowych podróży użytkowników uwierzytelniania przy użyciu zasad niestandardowych.

- Opisz przedziały użytkownika krok po kroku jako wymianę między dostawcami oświadczeń.

- Definiowanie rozgałęziania warunkowego w podróży użytkownika.

- Przekształcaj i Mapuj oświadczenia do użycia w decyzjach i komunikacji w czasie rzeczywistym.

- Korzystanie z usług z obsługą interfejsu API REST w przypadku niestandardowych podróży użytkowników uwierzytelniania. Na przykład z dostawcami poczty e-mail, CRMs i systemami autoryzacji własnościowej.

- Sfederować z dostawcami tożsamości, którzy są zgodni z protokołem OpenIDConnect. Na przykład z wieloma dzierżawcami usługi Azure AD, dostawcami kont społecznościowych lub dostawcami weryfikacji dwuskładnikowej.

Aby uzyskać więcej informacji na temat tworzenia zasad niestandardowych, zobacz [uwagi dla deweloperów dotyczące zasad niestandardowych w Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) i przeczytaj [wpis w blogu pozostałej Simon, w tym analizy przypadków](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji usługi Azure AD — marzec 2019

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm

W marcu 2019 dodaliśmy te 14 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [system inspekcji oparty na wyjaśnieniu](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Leaning](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), PowerSchool — zagadnienia dotyczące [wydajności](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode ](https://cinode.com/), [Sieć intranetowa Iris](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial) [, zadanie](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nowe łączniki inicjowania obsługi rozwiązania Zscaler i Atlassian w galerii usługi Azure AD — marzec 2019

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aprowizowanie aplikacji  
**Możliwość produktu:** Integracje innych firm

Automatyzuj tworzenie, aktualizowanie i usuwanie kont użytkowników dla następujących aplikacji:

[Rozwiązania Zscaler](https://aka.ms/ZscalerProvisioning), [rozwiązania Zscaler beta](https://aka.ms/ZscalerBetaProvisioning), [rozwiązania Zscaler One](https://aka.ms/ZscalerOneProvisioning), [rozwiązania Zscaler dwa](https://aka.ms/ZscalerTwoProvisioning), [rozwiązania Zscaler trzy](https://aka.ms/ZscalerThreeProvisioning), [rozwiązania Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć organizację przez automatyczne Inicjowanie obsługi kont użytkowników, zobacz [Automatyzowanie aprowizacji użytkowników w aplikacjach SaaS za pomocą usługi Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Przywracanie usuniętych grup programu Office 365 i zarządzanie nimi w portalu usługi Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Zarządzanie grupami  
**Możliwość produktu:** Współpraca

Można teraz wyświetlać usunięte grupy programu Office 365 i zarządzać nimi z poziomu portalu usługi Azure AD. Ta zmiana ułatwia sprawdzenie, które grupy są dostępne do przywrócenia, a także umożliwia trwałe usunięcie wszystkich grup, które nie są potrzebne w organizacji.

Aby uzyskać więcej informacji, zobacz [przywracanie wygasłych lub usuniętych grup](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Logowanie jednokrotne jest teraz dostępne dla aplikacji lokalnych opartych na protokole SAML usługi Azure AD za pomocą serwera proxy aplikacji (publiczna wersja zapoznawcza)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Serwer proxy aplikacji  
**Możliwość produktu:** Access Control

Teraz możesz udostępnić Logowanie jednokrotne (SSO) dla aplikacji lokalnych, uwierzytelnianych przy użyciu protokołu SAML oraz dostępu zdalnego do tych aplikacji za pomocą serwera proxy aplikacji. Aby uzyskać więcej informacji na temat sposobu konfigurowania logowania jednokrotnego SAML przy użyciu aplikacji lokalnych, zobacz Usługa [SAML Single Sign-in dla aplikacji lokalnych z serwerem proxy aplikacji (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Pętle aplikacji klienckich w żądaniach zostaną zakłócone w celu poprawy niezawodności i środowiska użytkownika

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Uwierzytelnienia (logowania)  
**Możliwość produktu:** Uwierzytelnianie użytkownika

Aplikacje klienckie mogą nieprawidłowo wystawiać setki tych samych żądań logowania w krótkim czasie. Te żądania, niezależnie od tego, czy zakończyły się powodzeniem, czy nie, przyczyniają się do słabego środowiska użytkownika i zwiększonego obciążenia dla dostawcy tożsamości, zwiększając opóźnienia dla wszystkich użytkowników i zmniejszając dostępność dostawcy tożsamości.

Ta aktualizacja wysyła `invalid_grant` błąd: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` do aplikacji klienckich, które wydają zduplikowane żądania wiele razy w krótkim czasie, poza zakresem normalnej operacji. Aplikacje klienckie, które napotykają ten problem, powinny wyświetlać interaktywny monit, co wymaga ponownego zalogowania użytkownika. Aby uzyskać więcej informacji na temat tej zmiany i sposobu naprawy aplikacji w przypadku wystąpienia tego błędu, zobacz [co nowego w uwierzytelnianiu?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nowe środowisko użytkownika dzienników inspekcji jest teraz dostępne

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Monitorowanie i raportowanie

Utworzyliśmy nową stronę **dzienników inspekcji** usługi Azure AD, która ułatwia zwiększenie czytelności i sposób wyszukiwania informacji. Aby wyświetlić stronę nowe **dzienniki inspekcji** , wybierz pozycję **dzienniki inspekcji** w sekcji **aktywność** usługi Azure AD.

![Nowa strona dzienników inspekcji z przykładowymi informacjami](media/whats-new/audit-logs-page.png)

Aby uzyskać więcej informacji na temat nowych **dzienników inspekcji** , zobacz [Raporty aktywności inspekcji w portalu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nowe ostrzeżenia i wskazówki pomagające w zapobieganiu przypadkowej blokady administratora z nieprawidłowo skonfigurowanymi zasadami dostępu warunkowego

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Ochrona i zabezpieczanie tożsamości

Aby zapobiec przypadkowemu zablokowaniu przez administratorów własnych dzierżaw za pomocą nieprawidłowo skonfigurowanych zasad dostępu warunkowego, zostały utworzone nowe ostrzeżenia i zaktualizowane wskazówki w Azure Portal. Aby uzyskać więcej informacji o nowych wskazówkach, zobacz [co to są zależności usługi w Azure Active Directory dostęp warunkowy](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Udoskonalone środowiska użytkowania dotyczące użytkowników końcowych na urządzeniach przenośnych

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Warunki użytkowania  
**Możliwość produktu:** Ład

Zaktualizowaliśmy nasze istniejące warunki użytkowania, aby pomóc w ulepszaniu sposobu przeglądania i wyrażania zgody na warunki użytkowania na urządzeniu przenośnym. Teraz możesz powiększać i pomniejszać, wrócić, pobrać informacje i wybierać hiperlinki. Aby uzyskać więcej informacji o zaktualizowanych warunkach użytkowania, zobacz [Azure Active Directory warunki użytkowania](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Dostępne są nowe środowisko pobierania dzienników aktywności usługi Azure AD

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Raportowanie  
**Możliwość produktu:** Monitorowanie i raportowanie

Teraz można pobrać duże ilości dzienników aktywności bezpośrednio z Azure Portal. Ta aktualizacja umożliwia:

- Pobierz do 250 000 wierszy.

- Otrzymuj powiadomienie po zakończeniu pobierania.

- Dostosuj nazwę pliku.

- Określ format danych wyjściowych — JSON lub CSV.

Aby uzyskać więcej informacji na temat tej funkcji [, zobacz Szybki Start: Pobierz raport inspekcji przy użyciu Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Zmiana powodująca niezgodność: Aktualizacje oceny warunku przy użyciu programu Exchange ActiveSync (EAS)

**Wprowadź** Plan zmiany  
**Kategoria usługi:** Dostęp warunkowy  
**Możliwość produktu:** Access Control

Jesteśmy w trakcie aktualizowania sposobu, w jaki program Exchange ActiveSync (EAS) ocenia następujące warunki:

- Lokalizacja użytkownika, na podstawie kraju, regionu lub adresu IP

- Ryzyko logowania

- Platforma urządzeń

Jeśli te warunki zostały wcześniej użyte w zasadach dostępu warunkowego, należy pamiętać, że zachowanie warunku może się zmienić. Na przykład jeśli w zasadach użyto wcześniej warunku lokalizacji użytkownika, zasady te mogą być teraz pomijane na podstawie lokalizacji użytkownika.

---

## <a name="february-2019"></a>Luty 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Szyfrowanie tokenu SAML usługi Azure AD z możliwością konfigurowania (publiczna wersja zapoznawcza) 

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** SSO

Teraz można skonfigurować dowolną obsługiwaną aplikację SAML do odbierania szyfrowanych tokenów SAML. Po skonfigurowaniu i użyciu z aplikacją usługa Azure AD szyfruje wyemitowane potwierdzenia SAML przy użyciu klucza publicznego uzyskanego z certyfikatu przechowywanego w usłudze Azure AD.

Aby uzyskać więcej informacji o konfigurowaniu szyfrowania tokenów SAML, zobacz [Konfigurowanie szyfrowania tokenów SAML usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Tworzenie przeglądu dostępu dla grup i aplikacji za pomocą przeglądów dostępu usługi Azure AD

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Kontrole uprawnień dostępu  
**Możliwość produktu:** Ład

Teraz można uwzględnić wiele grup lub aplikacji w jednym przeglądzie dostępu do usługi Azure AD na potrzeby członkostwa w grupie lub przypisywania aplikacji. Przeglądy dostępu z wieloma grupami lub aplikacjami są konfigurowane przy użyciu tych samych ustawień, a wszyscy włączeni recenzenci są powiadamiani w tym samym czasie.

Aby uzyskać więcej informacji na temat tworzenia przeglądu dostępu za pomocą przeglądów dostępu w usłudze Azure AD, zobacz [Tworzenie przeglądu dostępu do grup lub aplikacji w przeglądach dostępu do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nowe aplikacje federacyjne dostępne w galerii aplikacji Azure AD — luty 2019

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** Aplikacje dla przedsiębiorstw  
**Możliwość produktu:** Integracje innych firm
 
W lutym 2019 dodaliśmy następujące 27 nowych aplikacji z obsługą Federacji do galerii aplikacji:

[Euromonitor paszport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [odcisk palca](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7),, [stosy](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial)zawartości i [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Uprawnienie kliknij, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [sejsmiczne](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Udostępnij marzenie](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods Cloud Integration](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [wiedza System LMS z dowolnego miejsca](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [kampus w jednostce organizacyjnej](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [dane Periscope](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Portal NetOp](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.IO](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud przez Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [platforma produktywności ClickUp](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Aby uzyskać więcej informacji o aplikacjach, zobacz [integracji aplikacji SaaS w usłudze Azure Active Directory](https://aka.ms/appstutorial). Aby uzyskać więcej informacji o ofercie swoją aplikację w galerii aplikacji Azure AD, zobacz [umieść swoją aplikację w galerii aplikacji usługi Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Rozszerzona połączona rejestracja uwierzytelniania wieloskładnikowego lub samoobsługowego resetowania hasła

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** Samodzielne resetowanie hasła  
**Możliwość produktu:** Uwierzytelnianie użytkownika

W odpowiedzi na Opinie klientów Ulepszono środowisko w wersji zapoznawczej usługi MFA/SSPR, dzięki czemu użytkownicy mogą szybko rejestrować swoje informacje zabezpieczające dla usługi MFA i SSPR. 

**Aby włączyć już dziś udoskonalone środowisko dla użytkowników, wykonaj następujące kroki:**

1. Jako Administrator globalny lub administrator użytkownika Zaloguj się do Azure Portal i przejdź do pozycji **Azure Active Directory > Ustawienia użytkownika > Zarządzaj ustawieniami funkcji w wersji zapoznawczej panelu dostępu**. 

2. W przypadku **użytkowników, którzy mogą używać funkcji w wersji zapoznawczej do rejestrowania i zarządzania informacjami o zabezpieczeniach — odświeżanie** , wybierz opcję włączenia funkcji dla **wybranej grupy użytkowników** lub dla **wszystkich użytkowników**.

W ciągu najbliższych kilku tygodni usuniemy możliwość włączenia starego połączenia usługi SSPR w wersji zapoznawczej usługi MFA w przypadku dzierżawców, które nie są jeszcze włączone.

**Aby sprawdzić, czy formant zostanie usunięty dla dzierżawy, wykonaj następujące kroki:**

1. Jako Administrator globalny lub administrator użytkownika Zaloguj się do Azure Portal i przejdź do pozycji **Azure Active Directory > Ustawienia użytkownika > Zarządzaj ustawieniami funkcji w wersji zapoznawczej panelu dostępu**.  

2. Jeśli **Użytkownicy, którzy mogą korzystać z funkcji w wersji zapoznawczej do rejestrowania i zarządzania informacjami o zabezpieczeniach** , mają ustawioną wartość **Brak**, opcja zostanie usunięta z dzierżawy.

Bez względu na to, czy wcześniej włączono poprzednie środowisko w wersji zapoznawczej rejestracji usługi MFA/SSPR dla użytkowników, stare środowisko zostanie wyłączone w przyszłości. Ze względu na to zdecydowanie zalecamy przechodzenie do nowego, ulepszonego środowiska tak szybko, jak to możliwe.

Aby uzyskać więcej informacji na temat udoskonalonego środowiska rejestracji, zobacz [chłodne ulepszenia usługi Azure AD połączenia MFA i rejestracji resetowania haseł](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Zaktualizowano środowisko zarządzania zasadami przepływów użytkownika

**Wprowadź** Zmieniona funkcja  
**Kategoria usługi:** B2C — Consumer Identity Management  
**Możliwość produktu:** B2B/B2C

Zaktualizowaliśmy proces tworzenia i zarządzania zasadami dla przepływów użytkowników (wcześniej znanych jako wbudowane zasady). To nowe środowisko jest teraz domyślne dla wszystkich dzierżawców usługi Azure AD.

Dodatkowe opinie i sugestie można przekazać, korzystając z ikon uśmiech lub niezadowolenia w obszarze **Wyślij nam opinię** w górnej części ekranu portalu.

Aby uzyskać więcej informacji na temat nowego środowiska zarządzania zasadami, zapoznaj się z tematem [Azure AD B2C teraz zawiera on dostosowanie JavaScript i wiele nowych funkcji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) .

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Wybierz wersje elementów strony udostępnione przez usługę Azure AD B2C

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** B2C — Consumer Identity Management  
**Możliwość produktu:** B2B/B2C

Teraz można wybrać określoną wersję elementów strony dostarczonych przez Azure AD B2C. Wybierając określoną wersję, można przetestować aktualizacje, zanim zostaną one wyświetlone na stronie i można uzyskać przewidywalne zachowanie. Ponadto możesz teraz wyrazić zgodę na wymuszanie określonych wersji strony, aby umożliwić dostosowywanie języka JavaScript. Aby włączyć tę funkcję, przejdź do strony **Właściwości** w obszarze przepływy użytkownika.

Aby uzyskać więcej informacji na temat wybierania określonych wersji elementów strony, zapoznaj się z tematem [Azure AD B2C teraz zawiera on dostosowanie JavaScript i wiele nowych funkcji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) .

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Wymagania dotyczące hasła użytkownika końcowego z możliwością konfigurowania dla usługi B2C (ogólna dostępność)

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** B2C — Consumer Identity Management  
**Możliwość produktu:** B2B/B2C

Teraz możesz skonfigurować złożoność hasła organizacji dla użytkowników końcowych, zamiast korzystać z natywnych zasad haseł usługi Azure AD. W bloku **Właściwości** przepływów użytkownika (wcześniej znanych jako wbudowane zasady) można wybrać złożoność hasła lub **silne**lub można utworzyć **niestandardowy** zestaw wymagań .

Więcej informacji o konfiguracji wymagań dotyczących złożoności haseł znajduje się [w temacie Konfigurowanie wymagań dotyczących złożoności haseł w Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nowe szablony domyślne dla niestandardowych środowisk uwierzytelniania z oznakowaniem marki

**Wprowadź** Nowa funkcja  
**Kategoria usługi:** B2C — Consumer Identity Management  
**Możliwość produktu:** B2B/B2C

Możesz użyć naszych nowych szablonów domyślnych znajdujących się w bloku **układy stron** przepływów użytkownika (znanych wcześniej jako zasady wbudowane), aby utworzyć niestandardowe środowisko uwierzytelniania markowego dla użytkowników.

Aby uzyskać więcej informacji o korzystaniu z szablonów, zobacz [Azure AD B2C teraz zawiera ona dostosowanie JavaScript i wiele nowych funkcji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---
