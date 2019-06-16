---
title: Uwagi dla deweloperów dla niestandardowych zasad — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Uwagi dla deweloperów, od konfigurowania i konserwowania usługi Azure AD B2C za pomocą zasad niestandardowych.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1d0be4ec2ed8feb308839377e0494ef2f4b78368
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510212"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Uwagi dla deweloperów dla zasad niestandardowych w usłudze Azure Active Directory B2C

Niestandardowe zasady konfiguracji w usłudze Azure Active Directory B2C jest teraz ogólnie dostępna. Tej metody konfiguracji jest przeznaczona dla deweloperów tożsamości zaawansowanego tworzenia rozwiązań złożone tożsamości. Zasady niestandardowe udostępnić możliwości platformy środowiska tożsamości w dzierżawach usługi Azure AD B2C. Zaawansowane tożsamości deweloperów za pomocą zasad niestandardowych, należy zaplanować inwestować trochę czasu na ukończenie przewodników i odczytywanie dokumenty referencyjne.

Chociaż większość opcji zasad niestandardowych, dostępne są teraz ogólnie dostępne, ma funkcji podstawowych, takich jak profil techniczny typów i definicji zawartości interfejsów API, które są w różnych etapach cyklu życia oprogramowania. Nadchodzą wiele innych. W poniższej tabeli, określa poziom dostępności na bardziej szczegółowym poziomie.  

## <a name="features-that-are-generally-available"></a>Funkcje, które są ogólnie dostępne

- Tworzenie i przekazywanie niestandardowe uwierzytelnianie użytkownika podróży za pomocą zasad niestandardowych.  
    - Opisz podróży użytkownika krok po kroku, jak wymiany między dostawców oświadczeń.  
    - Zdefiniuj rozgałęzienia warunkowe w podróży użytkownika.  
- Współpraca z obsługą interfejsów API REST usług w Twojej podróży użytkownika uwierzytelniania niestandardowego.  
- Utworzyć Federację z dostawcami tożsamości, które są zgodne z protokołu OpenIDConnect.  
- Utworzyć Federację z dostawcami tożsamości, zgodne z protokołu SAML 2.0.   

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Obowiązki deweloperom zestaw funkcji zasad niestandardowych

Konfiguracja zasad ręczne udziela dostępu niższego poziomu dla podstawowej platformy usługi Azure AD B2C i wyników do utworzenia unikatowe, framework zaufania. Wiele możliwych kombinacji niestandardowych dostawców tożsamości, relacje zaufania, integracji z usługami zewnętrznymi i instrukcje krok po kroku przepływy pracy wymagają metodyczny podejście do projektowania i konfiguracji. 

Deweloperom korzystanie z zestawu funkcji niestandardowych zasad przestrzegać następujących wytycznych:

- Zapoznanie się z języka konfiguracji niestandardowych zasad oraz klucza lub kluczy tajnych zarządzania. Aby uzyskać więcej informacji, zobacz [elementu TrustFrameworkPolicy](trustframeworkpolicy.md). 
- Przejęcie na własność scenariuszy i niestandardowych integracji. Dokumentowanie swoją pracę i o organizacji działającej witryny.  
- Testowanie metodyczny scenariusza. 
- Postępuj zgodnie z tworzenia oprogramowania i przemieszczania najlepsze rozwiązania z co najmniej jednego środowiska deweloperskiego i testowego oraz jednego środowiska produkcyjnego. 
- Aktualne informacje na temat nowych rozwiązań od dostawcy tożsamości i usług, z których możesz zintegrować z usługą. Na przykład śledzenie pracy w wpisów tajnych i planowanych i nieplanowanych zmian wprowadzonych w usłudze. 
- Konfigurowanie aktywnego monitorowania i monitorować czas reakcji w środowisku produkcyjnym. Aby uzyskać więcej informacji na temat integracji z usługą Application Insights, zobacz [usługi Azure Active Directory B2C: Zbieranie dzienników](active-directory-b2c-custom-guide-eventlogger-appins.md). 
- Aktualizuj adresy e-mail kontaktu w subskrypcji platformy Azure i przygotuj reagują na wiadomości e-mail z działającej witryny zespołu firmy Microsoft. 
- Podejmij działanie aktualnych przypadku zaleca się, aby to zrobić przez zespół aktywnej witryny firmy Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Postanowienia dotyczące funkcji w publicznej wersji zapoznawczej

- Zachęcamy do użycia funkcji w publicznej wersji zapoznawczej tylko w celach testowych. 
- Umowy dotyczące poziomu usług (SLA) nie dotyczą funkcji w publicznej wersji zapoznawczej.
- Żądania pomocy technicznej dotyczące funkcji w publicznej wersji zapoznawczej można zgłosić za pośrednictwem kanałów pomocy technicznej regularne. 

## <a name="features-by-stage-and-known-issues"></a>Funkcje według etapów i znane problemy

Możliwości platformy środowiska tożsamości/zasady niestandardowej opracowywane są szybkie i stałych. Poniższa tabela jest indeks funkcji i dostępności składników.

### <a name="identity-providers-tokens-protocols"></a>Dostawcy tokenów, protokoły

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-------- | ----------- | ------- | -- | ----- |
| IDP-OpenIDConnect |  |  | X | Na przykład, Google +.  |
| IDP-OAUTH2 |  |  | X | Na przykład w serwisie Facebook.  |
| Dostawcy tożsamości — OAUTH1 (twitter) |  | X |  | Na przykład w usłudze Twitter. |
| Dostawcy tożsamości — OAUTH1 (na przykład twitter) |  |  |  | Nieobsługiwane |
| IDP-SAML |  |   | X | Na przykład, Salesforce, usług AD FS. |
| IDP-WSFED | X |  |  |  |
| Jednostki uzależnionej firm OAUTH1 |  |  |  | Nieobsługiwane. |
| Jednostki uzależnionej firm OAUTH2 |  |  | X |  |
| Jednostki uzależnionej firm OIDC |  |  | X |  |
| Jednostki uzależnionej SAML innych firm | X |  |  |  |
| Jednostki uzależnionej firm WSFED | X |  |  |  |
| Interfejs API REST, Basic i uwierzytelniania certyfikatów |  |  | X | Na przykład usługi Azure Logic Apps. |

### <a name="component-support"></a>Obsługa składników

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | ----------- | ------- | -- | ----- |
| Usługa Azure Multi-Factor Authentication |  |  | X |  |
| Usługa Azure Active Directory jako katalogu lokalnego |  |  | X |  |
| Usługa Azure podsystem poczty E-mail dla Weryfikacja adresu e-mail |  |  | X |  |
| Obsługa wielu języków|  |  | X |  |
| Sprawdzanie poprawności predykatu |  |  | X | Na przykład, złożoność hasła. |
| Korzystanie z dostawców usługi poczty e-mail innych firm | X |  |  |  |

### <a name="content-definition"></a>Definicję zawartości

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | ----------- | ------- | -- | ----- |
| Strona błędu, api.error |  |  | X |  |
| Strona wyboru dostawcy tożsamości, api.idpselections |  |  | X |  |
| Wybieranie dostawcy tożsamości do rejestracji, api.idpselections.signup |  |  | X |  |
| Nie pamiętasz hasła, api.localaccountpasswordreset |  |  | X |  |
| Lokalne konto logowania, api.localaccountsignin |  |  | X |  |
| Zarejestruj się, api.localaccountsignup konta lokalnego |  |  | X |  |
| Strona uwierzytelniania Wieloskładnikowego, api.phonefactor |  |  | X |  |
| Api.selfasserted tworzenia konta, samodzielnie konta społecznościowego |  |  | X |  |
| Samodzielnie określonych aktualizacji profilu api.selfasserted.profileupdate |  |  | X |  |
| Ujednolicone rejestracji lub strony logowania, api.signuporsignin z parametrem "disableSignup" |  |  | X |  |
| JavaScript / strony umowy |  | X |  |  |

### <a name="app-ief-integration"></a>Integracja aplikacji IEF

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | ----------- | ------- | -- | ----- |
| Element domain_hint parametr ciągu zapytania |  |  | X | Jest dostępna jako oświadczenia, mogą być przekazywane do dostawcy tożsamości. |
| Login_hint parametr ciągu zapytania |  |  | X | Jest dostępna jako oświadczenia, mogą być przekazywane do dostawcy tożsamości. |
| Wstawianie UserJourney JSON za pośrednictwem client_assertion | X |  |  | Staną się przestarzałe. |
| Wstawianie UserJourney JSON jako id_token_hint |  | X |  | Przejdź na następny podejście do przekazania JSON. |
| Przekaż TOKEN dostawcy tożsamości do aplikacji |  | X |  | Na przykład z usługi Facebook do aplikacji. |

### <a name="session-management"></a>Zarządzanie sesjami

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | ----------- | ------- | -- | ----- |
| Dostawcy sesji logowania jednokrotnego |  |  | X |  |
| Dostawcy sesji logowania zewnętrznego |  |  | X |  |
| Dostawcy sesji logowania jednokrotnego SAML |  |  | X |  |
| Domyślnego dostawcy sesji logowania jednokrotnego |  |  | X |  |

### <a name="security"></a>Bezpieczeństwo

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-------- | ----------- | ------- | -- | ----- |
| Zasady kluczy — Generowanie, ręczna, przekazywania |  |  | X |  |
| Zasady kluczy - RSA/Cert wpisów tajnych |  |  | X |  |
| Przekazywanie zasad |  |  | X |  |

### <a name="developer-interface"></a>Interfejs dla deweloperów

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | ----------- | ------- | -- | ----- |
| Azure Portal-IEF UX |  |  | X |  |
| Dzienniki UserJourney Insights aplikacji |  | X |  | Używane do rozwiązywania problemów w czasie projektowania.  |
| Dzienniki zdarzeń szczegółowe informacje w aplikacji (przy użyciu etapy aranżacji) |  | X |  | Używana do monitorowania przepływów użytkownika w środowisku produkcyjnym. |

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o niestandardowych zasad oraz różnice przy użyciu przepływów użytkownika](active-directory-b2c-overview-custom.md).
